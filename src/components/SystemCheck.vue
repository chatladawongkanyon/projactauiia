<template>
  <div class="container">
    <!-- หัวข้อ -->
    <h1 class="title">SYSTEM CHECK</h1>

    <!-- ตารางเซ็นเซอร์ -->
    <div class="table-container">
      <div class="table-wrapper">
        <table class="sensor-table">
          <thead>
            <tr>
              <th>Sensor</th>
              <th>Status</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="sensor in leftSensors" :key="sensor.id">
              <td>{{ sensor.name }}</td>
              <td>{{ sensor.status || "Waiting..." }}</td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="table-wrapper">
        <table class="sensor-table">
          <thead>
            <tr>
              <th>Sensor</th>
              <th>Status</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="sensor in rightSensors" :key="sensor.id">
              <td>{{ sensor.name }}</td>
              <td>{{ sensor.status || "Waiting..." }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- ปุ่ม Logout และ Dashboard -->
    <div class="button-container">
      <button @click="logout" class="logout-button">LOGOUT</button>
      <button @click="goToDashboard" class="dashboard-button">DASHBOARD</button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import mqtt from "mqtt";
import { auth } from "../firebaseConfig";

const router = useRouter();

// เชื่อมต่อ MQTT Broker
const client = mqtt.connect("wss://test.mosquitto.org:8081");

// ข้อมูลเซ็นเซอร์
const leftSensors = ref([
  { id: 1, name: "number 1", status: "" },
  { id: 2, name: "number 2", status: "" },
  { id: 3, name: "number 3", status: "" },
  { id: 4, name: "number 4", status: "" },
  { id: 5, name: "number 5", status: "" },
]);

const rightSensors = ref([
  { id: 6, name: "number 6", status: "" },
  { id: 7, name: "number 7", status: "" },
  { id: 8, name: "number 8", status: "" },
  { id: 9, name: "number 9 (Cars In Count-moter)", status: "" },
  { id: 10, name: "number 10 (Cars Out Count)", status: "" },
]);

// เชื่อมต่อและ Subscribe MQTT
client.on("connect", () => {
  console.log("✅ MQTT Connected to wss://test.mosquitto.org:8081");
  leftSensors.value.concat(rightSensors.value).forEach((sensor) => {
    client.subscribe(`kmitl/project/irsensor/${sensor.id}`, (err) => {
      if (!err) {
        console.log(`✅ Subscribed to kmitl/project/irsensor/${sensor.id}`);
      }
    });
  });
});

// อัปเดตข้อมูลจาก MQTT
client.on("message", (topic, message) => {
  const sensorNumber = parseInt(topic.replace("kmitl/project/irsensor/", ""));
  const statusValue = message.toString();

  let sensorList = [...leftSensors.value, ...rightSensors.value];
  const sensor = sensorList.find((s) => s.id === sensorNumber);
  if (sensor) sensor.status = statusValue;
});

// ตรวจสอบสถานะการล็อกอิน
onMounted(() => {
  if (!auth.currentUser) {
    router.push("/login");
  }
});

// ฟังก์ชันล็อกเอาต์
function logout() {
  auth.signOut()
    .then(() => {
      router.push("/login");
    })
    .catch((error) => {
      console.error("Sign out error:", error);
    });
}

// ฟังก์ชันไปหน้า Dashboard
function goToDashboard() {
  router.push("/dashboard");
}
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  background: white;
  padding: 20px;
}
.title {
  font-size: 28px;
  font-weight: bold;
  margin-bottom: 20px;
  text-transform: uppercase;
  color: #0047AB;
}
.table-container {
  display: flex;
  gap: 20px;
  width: 100%;
  justify-content: center;
}
.sensor-table {
  border-collapse: collapse;
  width: 100%;
  max-width: 400px;
  text-align: center;
  border: 2px solid #2E8B57;
}
.sensor-table th {
  background: #2E8B57;
  padding: 14px;
  color: white;
}
.sensor-table td {
  border: 1px solid #2E8B57;
  padding: 12px;
  background: #E0F7FA;
}
.button-container {
  display: flex;
  gap: 15px;
  margin-top: 20px;
}
.logout-button {
  background: linear-gradient(to right, #ff5733, #ff8d72);
  color: white;
  padding: 12px 22px;
  border-radius: 30px;
  transition: all 0.3s;
}
.dashboard-button {
  background: linear-gradient(to right, #b2f7ef, #7b8df2);
  color: black;
  padding: 12px 22px;
  border-radius: 30px;
  transition: all 0.3s;
}
</style>

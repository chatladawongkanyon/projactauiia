<template>
  <div class="container">
    <!-- สถานะที่จอดรถ -->
    <div class="status-container">
      <div class="status">
        <div class="status-box available"></div> Available
      </div>
      <div class="status">
        <div class="status-box occupied"></div> Occupied
      </div>
    </div>

    <!-- ตัวนับรถ -->
    <div class="counter-container">
      <span>Current Car Count: {{ carCount }}</span>
    </div>

    <!-- ตารางที่จอดรถ -->
    <div class="parking-grid">
      <div
        v-for="slot in parkingSlots"
        :key="slot.id"
        :class="['parking-slot', slot.occupied ? 'occupied' : 'available']"
      >
        {{ slot.name }}
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
const client = mqtt.connect("wss://test.mosquitto.org:8081");

const carCount = ref(0);
const lastEntryState = ref(0);
const lastExitState = ref(0);

const parkingSlots = ref([
  { id: 1, name: "P1", topic: "kmitl/project/irsensor/1", occupied: false },
  { id: 2, name: "P2", topic: "kmitl/project/irsensor/2", occupied: false },
  { id: 3, name: "P3", topic: "kmitl/project/irsensor/3", occupied: false },
  { id: 4, name: "P4", topic: "kmitl/project/irsensor/4", occupied: false },
  { id: 5, name: "P5", topic: "kmitl/project/irsensor/5", occupied: false },
  { id: 6, name: "P6", topic: "kmitl/project/irsensor/6", occupied: false },
  { id: 7, name: "P7", topic: "kmitl/project/irsensor/7", occupied: false },
  { id: 8, name: "P8", topic: "kmitl/project/irsensor/8", occupied: false },
]);

const entrySensorTopic = "kmitl/project/irsensor/9";
const exitSensorTopic = "kmitl/project/irsensor/10";

client.on("connect", () => {
  console.log("✅ MQTT Connected");
  [...parkingSlots.value.map(slot => slot.topic), entrySensorTopic, exitSensorTopic].forEach(topic => {
    client.subscribe(topic, (err) => {
      if (!err) {
        console.log(`✅ Subscribed to ${topic}`);
      }
    });
  });
});

client.on("message", (topic, message) => {
  const status = message.toString() === "1" ? 1 : 0;
  
  if (topic === entrySensorTopic) {
    if (status === 1 && lastEntryState.value === 0) {
      carCount.value += 1;
    }
    lastEntryState.value = status;
  } else if (topic === exitSensorTopic) {
    if (status === 1 && lastExitState.value === 0) {
      carCount.value = Math.max(0, carCount.value - 1);
    }
    lastExitState.value = status;
  } else {
    const slot = parkingSlots.value.find(s => s.topic === topic);
    if (slot) {
      slot.occupied = status === 1;
    }
  }
});

onMounted(() => {
  if (!auth.currentUser) {
    router.push("/login");
  }
});

function logout() {
  auth.signOut()
    .then(() => {
      router.push("/login");
    })
    .catch(error => {
      console.error("Sign out error:", error);
    });
}

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
  width: 100%;
  background: linear-gradient(to right, #3a7bd5, #3a6073);
  padding: 20px;
  box-sizing: border-box;
  color: white;
}

.status-container {
  display: flex;
  justify-content: left;
  width: 80%;
  margin-bottom: 15px;
}

.status-box {
  width: 20px;
  height: 20px;
  border-radius: 5px;
  margin-right: 10px;
}

.available {
  background-color: #28a745;
}

.occupied {
  background-color: #dc3545;
}

.counter-container {
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 20px;
}

.parking-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(100px, 1fr));
  grid-template-rows: repeat(2, minmax(100px, 1fr));
  gap: 10px;
  border-radius: 10px;
  background: rgba(255, 255, 255, 0.2);
  padding: 15px;
  box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.3);
}

.parking-slot {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 24px;
  font-weight: bold;
  border-radius: 10px;
  width: 100px;
  height: 100px;
  transition: background 0.3s ease;
  box-shadow: 2px 2px 6px rgba(0, 0, 0, 0.2);
}

.parking-slot.occupied {
  background: #dc3545;
  color: white;
}

.parking-slot.available {
  background: #28a745;
  color: white;
}
</style>

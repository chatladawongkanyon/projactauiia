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
      <span>Current Car Count: ___</span>
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
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import mqtt from "mqtt";
import { auth } from "../firebaseConfig";

const router = useRouter();

// ✅ ใช้ WebSocket Secure (`wss://test.mosquitto.org:8081`)
const client = mqtt.connect("wss://test.mosquitto.org:8081");

// **สถานะที่จอดรถ**
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

// **เชื่อมต่อและ Subscribe MQTT**
client.on("connect", () => {
  console.log("✅ MQTT Connected to wss://test.mosquitto.org:8081");
  parkingSlots.value.forEach((slot) => {
    client.subscribe(slot.topic, (err) => {
      if (!err) {
        console.log(`✅ Subscribed to ${slot.topic}`);
      }
    });
  });
});

// **อัปเดตสถานะจาก MQTT**
client.on("message", (topic, message) => {
  const status = message.toString() === "1"; // 1 = occupied, 0 = available
  const slot = parkingSlots.value.find((s) => s.topic === topic);
  if (slot) {
    slot.occupied = status;
  }
});

// **ตรวจสอบการล็อกอิน**
onMounted(() => {
  if (!auth.currentUser) {
    router.push("/login");
  }
});
</script>

<style scoped>
/* พื้นหลังที่มีสีสัน */
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

/* สถานะ Available & Occupied */
.status-container {
  display: flex;
  justify-content: left;
  width: 80%;
  margin-bottom: 15px;
}

.status {
  display: flex;
  align-items: center;
  margin-right: 20px;
  font-size: 18px;
}

.status-box {
  width: 20px;
  height: 20px;
  border-radius: 3px;
  margin-right: 5px;
}

.available {
  background-color: #28a745;
}

.occupied {
  background-color: #dc3545;
}

/* ตัวนับรถ */
.counter-container {
  display: flex;
  justify-content: space-around;
  width: 100%;
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 20px;
}

/* ตารางที่จอดรถ */
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

/* ปรับขนาดปุ่ม Logout และ Dashboard */
.button-container {
  position: absolute;
  bottom: 10px;
  right: 10px;
  display: flex;
  gap: 15px;
}

.logout-button {
  background: linear-gradient(to right, #ff5733, #ff8d72);
  color: white;
  font-weight: bold;
  border-radius: 20px;
  padding: 12px 24px;
  box-shadow: 3px 3px 10px rgba(0, 0, 0, 0.2);
  transition: 0.3s;
}

.logout-button:hover {
  background: linear-gradient(to right, #c70039, #ff5733);
}

.dashboard-button {
  background: linear-gradient(to right, #b2f7ef, #7b8df2);
  color: black;
  font-weight: bold;
  border-radius: 20px;
  padding: 12px 24px;
  box-shadow: 3px 3px 10px rgba(0, 0, 0, 0.2);
  transition: 0.3s;
}

.dashboard-button:hover {
  background: linear-gradient(to right, #5de6de, #7b8df2);
}

/* Responsive Design */
@media (max-width: 768px) {
  .parking-grid {
    grid-template-columns: repeat(4, minmax(80px, 1fr));
    grid-template-rows: repeat(2, minmax(80px, 1fr));
  }

  .parking-slot {
    width: 80px;
    height: 80px;
  }
}

@media (max-width: 480px) {
  .parking-grid {
    grid-template-columns: repeat(2, minmax(80px, 1fr));
    grid-template-rows: repeat(4, minmax(80px, 1fr));
  }

  .button-container {
    flex-direction: column;
    align-items: center;
  }
}
</style>

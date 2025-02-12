<template>
  <div class="container">
    <!-- Header with Rounded Bottom -->
    <div class="header">
      AUIIA CAR PARKING SYSTEMS
    </div>

    <!-- Dashboard Card -->
    <div class="dashboard-card">
      <h1 class="dashboard-title">DASHBORD</h1>

      <div class="menu">
        <router-link to="/parking-layout" class="menu-button">
          <img src="https://cdn-icons-png.flaticon.com/512/3202/3202926.png" alt="Parking Icon" class="icon" />
          PARKING LAYOUT
        </router-link>

        <router-link to="/system-check" class="menu-button">
          <img src="https://cdn-icons-png.flaticon.com/512/622/622669.png" alt="Magnifying Glass" class="icon" />
          SYSTEM CHECK
        </router-link>
      </div>
    </div>

    <!-- Logout Button -->
    <button @click="logout" class="logout-button">
      LOGOUT
    </button>
  </div>
</template>

<script lang="ts">
import { getAuth } from 'firebase/auth';
import { useRouter } from 'vue-router';

export default {
  name: 'Dashboard',
  setup() {
    const router = useRouter();
    const auth = getAuth();

    const logout = () => {
      auth.signOut().then(() => {
        router.push('/login');
      }).catch((error) => {
        console.error('Sign out error:', error);
      });
    };

    return {
      logout,
    };
  },
};
</script>

<style scoped>
/* Main Container */
.container {
  display: flex;
  flex-direction: column;
  justify-content: center; /* ให้แดชบอร์ดอยู่กึ่งกลางหน้าจอ */
  align-items: center;
  min-height: 100vh;
  width: 100%;
  background: white;
  position: relative;
  padding: 20px;
  box-sizing: border-box;
}

/* Header - ติดขอบบน */
.header {
  width: 100%;
  background: #0047AB;
  color: white;
  font-weight: bold;
  text-align: center;
  font-size: 20px;
  padding: 15px;
  border-bottom-left-radius: 25px;
  border-bottom-right-radius: 25px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  position: absolute;
  top: 0;
}

/* Dashboard Card */
.dashboard-card {
  background: linear-gradient(to right, #6a5acd, #ff69b4);
  border-radius: 20px;
  padding: 30px;
  width: 90%;
  max-width: 400px;
  text-align: center;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

/* Title */
.dashboard-title {
  color: white;
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 15px;
}

/* Menu Buttons */
.menu {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.menu-button {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: white;
  color: #a33b8f;
  font-size: 16px;
  font-weight: bold;
  padding: 12px;
  border-radius: 10px;
  transition: 0.3s;
  width: 100%;
}

.menu-button:hover {
  background-color: #f3e5f5;
}

.icon {
  width: 28px;
  margin-right: 10px;
}

/* Logout Button - อยู่มุมล่างขวา */
.logout-button {
  position: absolute;
  bottom: 20px;
  right: 20px;
  background: linear-gradient(to right, #ff5733, #ff8d72);
  color: white;
  font-weight: bold;
  border-radius: 20px;
  padding: 12px 24px;
  box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.2);
  transition: 0.3s;
}

.logout-button:hover {
  background: linear-gradient(to right, #c70039, #ff5733);
}

/* Responsive Design */
@media (max-width: 1024px) {
  .dashboard-card {
    max-width: 380px;
  }
}

@media (max-width: 768px) {
  .dashboard-card {
    max-width: 350px;
  }

  .menu-button {
    font-size: 14px;
    padding: 10px;
  }

  .icon {
    width: 25px;
  }
}

@media (max-width: 480px) {
  .dashboard-title {
    font-size: 20px;
  }

  .dashboard-card {
    max-width: 90%;
  }

  .menu-button {
    font-size: 14px;
    padding: 8px;
  }

  .icon {
    width: 22px;
  }

  .logout-button {
    padding: 10px 18px;
    font-size: 14px;
  }
}
</style>

<template>
  <div id="app">
    <header v-if="!ownerMode">
      <a @click="currentView = 'HomePage'" class="logo-link">
        <img src="@/assets/Logo.png" alt="AirSnS Logo" class="logo" />
      </a>
      <nav>
        <button @click="currentView = 'LoginPage'">Login</button>
        <button @click="currentView = 'CampingSpots'">Camping Spots</button>
        <button @click="currentView = 'ProfilePage'">Profile</button>
      </nav>
    </header>
    <main>
      <!-- Toon de juiste component op basis van de status -->
      <component :is="currentView" v-if="!ownerMode" @setOwnerMode="setOwnerMode" />
      <OwnerDashboard v-else />
    </main>
  </div>
</template>

<script>
import HomePage from './components/HomePage.vue';
import LoginPage from './components/LoginPage.vue';
import CampingSpotsPage from './components/CampingSpotspage.vue';
import ProfilePage from './components/ProfilePage.vue';
import OwnerDashboard from './components/OwnerDashboard.vue';

export default {
  components: {
    HomePage,
    LoginPage,
    CampingSpotsPage,
    ProfilePage,
    OwnerDashboard,
  },
  data() {
    return {
      currentView: 'HomePage', // Standaard component
      ownerMode: false, // Controle voor eigenaren
    };
  },
  methods: {
    // Methode om over te schakelen naar eigenaarmodus
    setOwnerMode(value) {
      this.ownerMode = value;
    },
  },
};
</script>

<style>
/* Styling om de afbeelding en navigatie netjes weer te geven */
header {
  display: flex;
  align-items: center;
  padding: 10px;
}

.logo-link {
  cursor: pointer;
}

.logo {
  height: 70px; /* Pas aan naar de gewenste grootte */
  margin-right: 0px;
}

nav button {
  margin-left: 10px;
  background-color: #5c0a5c;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 8px 15px;
  cursor: pointer;
  font-size: 14px;
}

nav button:hover {
  background-color: #901090;
  color: white;
}
</style>

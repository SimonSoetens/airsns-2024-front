<template>
  <div id="app">
    <header>
      <a @click="currentView = 'HomePage'" class="logo-link">
        <img src="@/assets/Logo.png" alt="AirSnS Logo" class="logo" />
      </a>
      <nav>
        <button @click="currentView = 'LoginPage'">Inloggen</button>
        <button @click="currentView = 'CampingSpotspage'">Kampeerplekken</button>
        <button @click="currentView = 'ProfilePage'">Profiel</button>
        <button v-if="ownerMode" @click="currentView = 'OwnerDashboard'">Beheren</button>
      </nav>
    </header>
    <main>
      <component :is="currentView" @setOwnerMode="setOwnerMode" />
    </main>
  </div>
</template>

<script>
import HomePage from './components/HomePage.vue';
import LoginPage from './components/LoginPage.vue';
import CampingSpotspage from './components/CampingSpotspage.vue';
import ProfilePage from './components/ProfilePage.vue';
import OwnerDashboard from './components/OwnerDashboard.vue';

export default {
  components: {
    HomePage,
    LoginPage,
    CampingSpotspage,
    ProfilePage,
    OwnerDashboard,
  },
  data() {
    return {
      currentView: 'HomePage',
      ownerMode: false, // Controle voor eigenaren
    };
  },
  methods: {
    setOwnerMode(value) {
      this.ownerMode = value; // Zet eigenaarmodus aan
    },
  },
};
</script>

<style>
/* Styling voor de app */
header {
  display: flex;
  align-items: center;
  padding: 10px;
}

.logo-link {
  cursor: pointer;
}

.logo {
  height: 70px;
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

body {
  background-image: url('assets/Background.webp'); 
  background-size: cover; /* Zorgt ervoor dat de afbeelding de hele achtergrond bedekt */
  background-position: center 100%; 
  background-repeat: no-repeat; /* Voorkomt herhaling van de afbeelding */
  background-attachment: fixed; 
}

nav button:hover {
  background-color: #901090;
}

main {
  padding: 20px;
}
</style>

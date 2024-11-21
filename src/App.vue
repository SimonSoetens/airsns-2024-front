<template>
  <div id="app">
    <header>
      <h1>AirSnS</h1>
      <nav>
        <button @click="currentView = 'HomePage'">Home</button>
        <button @click="currentView = 'LoginPage'">Login</button>
        <button @click="currentView = 'CampingSpots'">Camping Spots</button>
        <button @click="currentView = 'ProfilePage'">Profile</button>
      </nav>
    </header>

    <main>
      <component :is="currentView" />
    </main>
  </div>
</template>

<script>
import HomePage from './components/HomePage.vue';
import LoginPage from './components/LoginPage.vue';
import CampingSpotsPage from './components/CampingSpotspage.vue';
import ProfilePage from './components/ProfilePage.vue';

export default {
  components: {
    HomePage,
    LoginPage,
    CampingSpotsPage,
    ProfilePage,
  },
  data() {
    return {
      currentView: 'Homepage', // Standaard component
    };
  },
};
</script>

<style>
/* Voeg je CSS-styling hier toe */
</style>

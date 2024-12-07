<template>
  <div id="profile-page">
    <h1>Profielpagina</h1>
    <div v-if="user">
      <p><strong>Naam:</strong> {{ user.name }}</p>
      <p><strong>Voornaam:</strong> {{ user.firstname }}</p>
      <p><strong>E-mail:</strong> {{ user.email }}</p>
      <p><strong>Telefoonnummer:</strong> {{ user.phone }}</p>
      <p><strong>Geboortedatum:</strong> {{ user.date_of_birth }}</p>
      <p><strong>Land:</strong> {{ user.country }}</p>
    </div>
    <div v-else>
      <p>Gebruikersinformatie wordt geladen...</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      user: null,
    };
  },
  mounted() {
    const userId = localStorage.getItem("userId"); // Haal userId op uit localStorage
    if (userId) {
      axios
        .get(`http://localhost:3000/api/profile/${userId}`)
        .then((response) => {
          if (response.data.success) {
            this.user = response.data.user;
          } else {
            alert("Gebruikersinformatie niet gevonden.");
          }
        })
        .catch((error) => {
          console.error("Fout bij ophalen gebruikersinformatie:", error);
        });
    } else {
      alert("Je bent niet ingelogd.");
    }
  },
};
</script>


<style>
.profile-container {
  max-width: 600px;
  margin: 0 auto;
  font-family: Arial, sans-serif;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

p {
  font-size: 16px;
  line-height: 1.5;
  margin: 10px 0;
}

strong {
  color: #333;
}
</style>

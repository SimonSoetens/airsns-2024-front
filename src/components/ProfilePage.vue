<template>
  <div id="profile-page">
    <h1>Profiel</h1>
    <div v-if="user">
      <!-- Weergave modus -->
      <div v-if="!editMode">
        <p><strong>Naam:</strong> {{ user.name }}</p>
        <p><strong>Voornaam:</strong> {{ user.firstname }}</p>
        <p><strong>E-mail:</strong> {{ user.email }}</p>
        <p><strong>Telefoonnummer:</strong> {{ user.phone }}</p>
        <p><strong>Geboortedatum:</strong> {{ user.date_of_birth }}</p>
        <p><strong>Land:</strong> {{ user.country }}</p>
        <button @click="startEdit">Profiel bewerken</button>
      </div>

      <!-- Bewerken profiel modus -->
      <form v-else @submit.prevent="updateProfile" class="form-style">
        <label for="name">Naam:</label>
        <input id="name" v-model="user.name" type="text" required />

        <label for="firstname">Voornaam:</label>
        <input id="firstname" v-model="user.firstname" type="text" required />

        <label for="email">E-mail:</label>
        <input id="email" v-model="user.email" type="email" required />

        <label for="phone">Telefoonnummer:</label>
        <input id="phone" v-model="user.phone" type="text" required />

        <label for="date_of_birth">Geboortedatum:</label>
        <input id="date_of_birth" v-model="user.date_of_birth" type="date" required />

        <label for="country">Land:</label>
        <select id="country" v-model="user.country" required>
          <option v-for="country in countries" :key="country" :value="country">
            {{ country }}
          </option>
        </select>

        <button type="submit" class="save-button">Opslaan</button>
        <button type="button" @click="cancelEdit" class="cancel-button">Annuleren</button>
      </form>

      <p v-if="successMessage" class="success-message">{{ successMessage }}</p>
      <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>

      <!-- Uitlogknop -->
      <button class="logout-button" @click="logout">Uitloggen</button>
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
      originalUser: null, // Kopie van originele gegevens
      editMode: false,
      countries: [
        "Afghanistan",
        "Albania",
        "Algeria",
        "Andorra",
        "Angola",
        "Argentina",
        "Armenia",
        "Australia",
        "Austria",
        "Belgium",
        "Brazil",
        "Canada",
        "China",
        "France",
        "Germany",
        "India",
        "Italy",
        "Japan",
        "Mexico",
        "Netherlands",
        "Norway",
        "Pakistan",
        "Russia",
        "South Africa",
        "Spain",
        "Sweden",
        "Switzerland",
        "Turkey",
        "United Kingdom",
        "United States",
      ],
      successMessage: "",
      errorMessage: "",
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
            this.originalUser = { ...this.user }; // Maak een kopie van de originele gegevens
          } else {
            this.errorMessage = "Gebruikersinformatie niet gevonden.";
          }
        })
        .catch((error) => {
          console.error("Fout bij ophalen gebruikersinformatie:", error);
          this.errorMessage = "Er is een fout opgetreden bij het laden van de gebruikersinformatie.";
        });
    } else {
      this.errorMessage = "Je bent niet ingelogd.";
    }
  },
  methods: {
    startEdit() {
      this.editMode = true;
      this.originalUser = { ...this.user }; // Sla de huidige gegevens op voordat je gaat bewerken
    },
    cancelEdit() {
      this.user = { ...this.originalUser }; // Herstel de originele gegevens
      this.editMode = false;
      this.successMessage = ""; // Wis succesmelding
      this.errorMessage = ""; // Wis foutmelding
    },
    updateProfile() {
      const userId = localStorage.getItem("userId"); // Haal userId op
      if (userId) {
        axios
          .put(`http://localhost:3000/api/profile/${userId}`, this.user)
          .then((response) => {
            if (response.data.message === "User updated successfully") {
              this.successMessage = "Profiel succesvol bijgewerkt!";
              this.errorMessage = "";
              this.editMode = false; // Schakel terug naar weergave modus
            } else {
              this.successMessage = "";
              this.errorMessage = "Bijwerken mislukt. Probeer het opnieuw.";
            }
          })
          .catch((error) => {
            console.error("Fout bij het bijwerken van het profiel:", error);
            this.successMessage = "";
            this.errorMessage = "Er is een fout opgetreden bij het bijwerken.";
          });
      } else {
        this.errorMessage = "Je bent niet ingelogd.";
      }
    },
    logout() {
      localStorage.removeItem("userId"); // Verwijder userId uit localStorage
      window.location.href = "/login"; // Verwijs naar de loginpagina
    },
  },
};
</script>

<style>
#profile-page {
  max-width: 600px;
  margin: 0; /* Zorg ervoor dat er geen centrale uitlijning is */
  font-family: Arial, sans-serif;
  padding: 20px; /* Voeg een beetje padding toe voor ruimte aan de randen */
}

h1 {
  text-align: left; /* Houd de titel links uitgelijnd */
}

p {
  font-size: 16px;
  line-height: 1.5;
  margin: 10px 0;
  text-align: left; /* Zorg ervoor dat de tekst links uitgelijnd is */
}

button {
  margin-top: 10px;
  padding: 10px 20px;
  font-size: 16px;
  background-color: #5c0a5c;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  text-align: center; /* Tekst in de knoppen centreren */
}

button:hover {
  background-color: #901090;
}

.save-button,
.cancel-button {
  max-width: 400px; /* Zorg ervoor dat beide knoppen even breed zijn */
  width: 100%; /* Gebruik de volledige breedte binnen de container */
}

.logout-button {
  max-width: 400px; /* Zorg dat de uitlogknop dezelfde breedte heeft als andere knoppen */
  width: 27%; /* Gebruik de volledige breedte */
  margin-top: 20px;
  background-color: #c9302c; /* Roodachtige kleur voor de uitlogknop */
}

.logout-button:hover {
  background-color: #d9534f; /* Lichtere roodachtige kleur bij hover */
}

.form-style {
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-width: 100%; /* Gebruik de volledige breedte */
  text-align: left; /* Links uitlijnen */
}

label {
  font-weight: bold;
  text-align: left; /* Labels links uitlijnen */
}

input,
select {
  padding: 8px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  text-align: left; /* Tekst in de invoervelden links uitlijnen */
}

.success-message,
.error-message {
  text-align: left; /* Zorg dat de berichten links uitgelijnd zijn */
  margin-top: 20px;
  font-weight: bold;
}

.success-message {
  color: green;
}

.error-message {
  color: red;
}
</style>

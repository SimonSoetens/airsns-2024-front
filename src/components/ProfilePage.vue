<template>
  <div id="profile-page">
    <h1>Profiel</h1>
    <div v-if="user">
      <div class="tabs">
        <button :class="{ active: activeTab === 'gegevens' }" @click="activeTab = 'gegevens'">
          Gegevens
        </button>
        <button :class="{ active: activeTab === 'boekingen' }" @click="activeTab = 'boekingen'">
          Mijn boekingen
        </button>
      </div>

      <!-- Tab Gegevens -->
      <div v-if="activeTab === 'gegevens'">
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

        <button class="logout-button" @click="logout">Uitloggen</button>
      </div>

      <!-- Tab Mijn Boekingen -->
      <div v-if="activeTab === 'boekingen'">
        <h2>Mijn Boekingen</h2>
        <div v-if="bookings.length" class="bookings-container"> 
          <div class="booking-card" v-for="booking in bookings" :key="booking.booking_id"> 
            <h3>{{ booking.name || 'Geen naam beschikbaar' }}</h3>
            <p>{{ booking.description || 'Geen beschrijving beschikbaar' }}</p>
            <p><strong>Prijs per nacht:</strong> €{{ booking.price || 'Onbekend' }}</p>
          </div>


        </div>
        <p v-else>Geen boekingen gevonden.</p>
      </div>
    </div>

    <div v-else>
      <p>U bent niet ingelogd</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      user: null,
      originalUser: null,
      editMode: false,
      activeTab: 'gegevens', 
      bookings: [], 
      countries: [
        "Afghanistan",
        "Albanië",
        "Algerije",
        "Andorra",
        "Angola",
        "Argentinië",
        "Armenië",
        "Australië",
        "Oostenrijk",
        "België",
        "Brazilië",
        "Canada",
        "China",
        "Frankrijk",
        "Duitsland",
        "India",
        "Italië",
        "Japan",
        "Mexico",
        "Nederland",
        "Noorwegen",
        "Pakistan",
        "Rusland",
        "Zuid-Afrika",
        "Spanje",
        "Zweden",
        "Zwitserland",
        "Turkije",
        "Verenigd Koninkrijk",
        "Verenigde Staten",
      ],
      successMessage: "",
      errorMessage: "",
    };
  },
  mounted() {
    const userId = localStorage.getItem("userId");
    console.log("Loaded userId from localStorage:", userId);
    if (userId) {
      axios
        .get(`http://localhost:3000/api/profile/${userId}`)
        .then((response) => {
          console.log("Profile response:", response.data);
          if (response.data.success) {
            this.user = response.data.user;
            this.originalUser = { ...this.user };
          } else {
            this.errorMessage = "Gebruikersinformatie niet gevonden.";
          }
        })
        .catch((error) => {
          console.error("Fout bij ophalen gebruikersinformatie:", error);
          this.errorMessage = "Er is een fout opgetreden bij het laden van de gebruikersinformatie.";
        });

      // Fetch bookings for the user
      axios
      .get(`http://localhost:3000/api/bookings/${userId}`)
      .then((response) => { //als verzoek succesvol (server stuurt response terug)
        console.log("Bookings response:", response.data); 
        if (response.data.success) { 
          this.bookings = response.data.bookings; //lijst van boekingen opslagen in Vue-component
          console.log("Bookings ontvangen in frontend:", this.bookings);
        } else {
          console.error("Geen boekingen gevonden.");
        }
      })
      .catch((error) => {
        console.error("Fout bij ophalen boekingen:", error);
      });
    } else {
      this.errorMessage = "Je bent niet ingelogd.";
    }
  },
  methods: {
    startEdit() {
      console.log("Entering edit mode with user data:", this.user);
      this.editMode = true;
      this.originalUser = { ...this.user };
    },
    cancelEdit() {
      console.log("Cancelling edit. Restoring original user data:", this.originalUser);
      this.user = { ...this.originalUser };
      this.editMode = false;
      this.successMessage = "";
      this.errorMessage = "";
    },
    updateProfile() {
      const userId = localStorage.getItem("userId");
      console.log("Updating profile for userId:", userId);
      if (userId) {
        axios
          .put(`http://localhost:3000/api/profile/${userId}`, this.user)
          .then((response) => {
            console.log("Update profile response:", response.data);
            if (response.data.message === "User updated successfully") {
              this.successMessage = "Profiel succesvol bijgewerkt!";
              this.errorMessage = "";
              this.editMode = false;
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
      console.log("User logging out.");
      localStorage.removeItem("userId");
      window.location.href = "/";
    },
  },
};
</script>

<style>
#profile-page {
  max-width: 600px;
  margin: 0 auto;
  font-family: Arial, sans-serif;
  padding: 20px;
}

.tabs {
  display: flex;
  margin-bottom: 20px;
}

.tabs button {
  flex: 1;
  padding: 10px;
  background-color: #f5f5f5;
  border: 1px solid #ddd;
  cursor: pointer;
  font-weight: bold;
}

.tabs button.active {
  background-color: #5c0a5c;
  color: white;
}

.tabs button:hover {
  background-color: #901090;
  color: white;
}

.bookings-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  padding: 20px;
}

.booking-card {
  background-color: #f9f9f9;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  padding: 15px;
  width: 300px;
  text-align: left;
}

.booking-card h3 {
  margin: 0 0 10px;
  font-size: 18px;
  color: #5c0a5c;
}

.booking-card p {
  margin: 5px 0;
  font-size: 14px;
  color: #333;
}

p {
  font-size: 16px;
  line-height: 1.5;
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
}

button:hover {
  background-color: #901090;
}

.success-message {
  color: green;
  font-weight: bold;
}

.error-message {
  color: red;
  font-weight: bold;
}

.save-button,
.cancel-button {
  max-width: 400px;
  width: 100%;
}

.logout-button {
  background-color: #c9302c;
}

.logout-button:hover {
  background-color: #d9534f;
}
</style>
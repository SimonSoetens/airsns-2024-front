<template>
  <div id="owner-dashboard">
    <h1>Welkom, eigenaar!</h1>
    <p>Dit is je dashboard waar je camping spots kunt beheren.</p>
    <button @click="showAddForm = true" class="add-button">Campingspot toevoegen</button>

    <!-- Lijst van campingspots -->
    <div class="camping-list">
      <h2>Mijn Campingspots</h2>
      <ul>
        <li v-for="spot in campingSpots" :key="spot.spot_id">
          <div class="spot-details">
            <h3>{{ spot.name }}</h3>
            <p><strong>Locatie:</strong> {{ spot.location }}</p>
            <p><strong>Beschrijving:</strong> {{ spot.description }}</p>
            <p><strong>Prijs per nacht:</strong> €{{ spot.price }}</p>
          </div>
          <button class="delete-button" @click="deleteCampingSpot(spot.spot_id)">
            🗑️
          </button>
        </li>
      </ul>
      <p v-if="campingSpots.length === 0">Geen campingspots gevonden.</p>
    </div>

    <!-- Modaal venster voor het toevoegen van een campingspot -->
    <div v-if="showAddForm" class="modal">
      <div class="modal-content">
        <h2>Nieuwe campingspot toevoegen</h2>
        <form @submit.prevent="submitCampingSpot">
          <label for="name">Naam van de campingspot:</label>
          <input id="name" v-model="newCampingSpot.name" type="text" required />

          <label for="location">Locatie:</label>
          <input id="location" v-model="newCampingSpot.location" type="text" required />

          <label for="description">Beschrijving:</label>
          <textarea id="description" v-model="newCampingSpot.description" required></textarea>

          <label for="price">Prijs per nacht:</label>
          <input id="price" v-model="newCampingSpot.price" type="number" min="0" required />

          <button type="submit" class="save-button">Opslaan</button>
          <button type="button" @click="closeForm" class="cancel-button">Annuleren</button>
        </form>
        <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>
        <p v-if="successMessage" class="success-message">{{ successMessage }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "OwnerDashboard",
  data() {
    return {
      showAddForm: false, // Toont of verbergt het formulier
      campingSpots: [], // Lijst met campingspots
      newCampingSpot: {
        name: "",
        location: "",
        description: "",
        price: null,
      },
      errorMessage: "",
      successMessage: "",
    };
  },
  created() {
    this.fetchCampingSpots();
  },
  methods: {
    async fetchCampingSpots() {
      try {
        const response = await axios.get("http://localhost:3000/api/campingspots");
        if (response.data.success) {
          this.campingSpots = response.data.campingSpots;
        } else {
          this.errorMessage = "Kon campingspots niet ophalen.";
        }
      } catch (error) {
        console.error("Fout bij het ophalen van campingspots:", error.response?.data || error.message);
        this.errorMessage = "Er is een fout opgetreden bij het ophalen van campingspots.";
      }
    },
    async submitCampingSpot() {
      try {
        const campingSpotData = {
          name: this.newCampingSpot.name || null,
          location: this.newCampingSpot.location || null,
          description: this.newCampingSpot.description || null,
          price: this.newCampingSpot.price || null,
        };

        const response = await axios.post("http://localhost:3000/api/campingspots", campingSpotData);
        if (response.data.success) {
          this.successMessage = `Campingspot "${this.newCampingSpot.name}" is succesvol toegevoegd!`;
          this.fetchCampingSpots(); // Herlaad de lijst
          this.closeForm();
        } else {
          this.errorMessage = `Fout bij het toevoegen van campingspot: ${response.data.message}`;
        }
      } catch (error) {
        console.error("Fout bij het toevoegen van campingspot:", error.response?.data || error.message);
        this.errorMessage = "Er is een fout opgetreden. Probeer het opnieuw.";
      }
    },
    async deleteCampingSpot(spotId) {
      try {
        const response = await axios.delete(`http://localhost:3000/api/campingspots/${spotId}`);
        if (response.data.success) {
          this.successMessage = "Campingspot succesvol verwijderd!";
          this.fetchCampingSpots(); // Herlaad de lijst
        } else {
          this.errorMessage = "Kon campingspot niet verwijderen.";
        }
      } catch (error) {
        console.error("Fout bij het verwijderen van campingspot:", error.response?.data || error.message);
        this.errorMessage = "Er is een fout opgetreden. Probeer het opnieuw.";
      }
    },
    closeForm() {
      this.showAddForm = false;
      this.resetForm();
    },
    resetForm() {
      this.newCampingSpot = {
        name: "",
        location: "",
        description: "",
        price: null,
      };
      this.errorMessage = "";
      this.successMessage = "";
    },
  },
};
</script>

<style>
/* Voeg hier de styling toe */
.camping-list ul {
  list-style: none;
  padding: 0;
}

.camping-list li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border: 1px solid #ccc;
  padding: 10px;
  margin-bottom: 10px;
}

.delete-button {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 18px;
  color: red;
}

.delete-button:hover {
  color: darkred;
}
</style>

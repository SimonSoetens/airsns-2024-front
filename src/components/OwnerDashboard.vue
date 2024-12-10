<template>
  <div id="owner-dashboard">
    <h1>Welkom, eigenaar!</h1>
    <p>Dit is je dashboard waar je camping spots kunt beheren.</p>
    <button @click="showAddForm = true" class="add-button">Campingspot toevoegen</button>

    <!-- Lijst met campingspots -->
    <div v-if="campingSpots.length > 0" class="camping-list">
      <h2>Jouw Campingspots</h2>
      <ul>
        <li v-for="spot in campingSpots" :key="spot.spot_id">
          <h3>{{ spot.name }}</h3>
          <p><strong>Locatie:</strong> {{ spot.location }}</p>
          <p><strong>Beschrijving:</strong> {{ spot.description }}</p>
          <p><strong>Prijs per nacht:</strong> €{{ spot.price }}</p>
        </li>
      </ul>
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
      newCampingSpot: {
        name: "",
        location: "",
        description: "",
        price: null,
      },
      campingSpots: [], // Lijst van campingspots
      errorMessage: "",
      successMessage: "",
    };
  },
  methods: {
    async fetchCampingSpots() {
      try {
        const response = await axios.get("http://localhost:3000/api/campingspots");
        if (response.data.success) {
          this.campingSpots = response.data.campingSpots;
        } else {
          console.error("Fout bij het ophalen van campingspots:", response.data.message);
        }
      } catch (error) {
        console.error("Fout bij het ophalen van campingspots:", error.response?.data || error.message);
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

        if (!this.newCampingSpot.name || !this.newCampingSpot.location || !this.newCampingSpot.description || !this.newCampingSpot.price) {
          this.errorMessage = "Alle velden zijn verplicht!";
          return;
        }

        if (response.data.success) {
          this.successMessage = `Campingspot "${this.newCampingSpot.name}" is succesvol toegevoegd!`;
          this.fetchCampingSpots(); // Lijst opnieuw ophalen na toevoegen
          this.closeForm();
        } else {
          this.errorMessage = `Fout bij het toevoegen van campingspot: ${response.data.message}`;
        }
      } catch (error) {
        console.error("Fout bij het toevoegen van campingspot:", error.response?.data || error.message);
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
  mounted() {
    this.fetchCampingSpots(); // Campingspots ophalen bij het laden van de pagina
  },
};
</script>

<style>
#owner-dashboard {
  padding: 20px;
  font-family: Arial, sans-serif;
}

.add-button {
  margin-top: 20px;
  padding: 10px 20px;
  font-size: 16px;
  background-color: #5c0a5c;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.add-button:hover {
  background-color: #901090;
}

.camping-list {
  margin-top: 20px;
}

.camping-list ul {
  list-style-type: none;
  padding: 0;
}

.camping-list li {
  margin-bottom: 20px;
  padding: 15px;
  border: 1px solid #ccc;
  border-radius: 8px;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-content {
  background: white;
  padding: 20px;
  border-radius: 8px;
  width: 400px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

input,
textarea {
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: 100%;
  box-sizing: border-box;
}

textarea {
  resize: vertical;
  height: 100px;
}

.save-button {
  background-color: #5c0a5c;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 10px;
  cursor: pointer;
  font-size: 16px;
}

.save-button:hover {
  background-color: #901090;
}

.cancel-button {
  background-color: #ccc;
  color: black;
  border: none;
  border-radius: 5px;
  padding: 10px;
  cursor: pointer;
  font-size: 16px;
}

.cancel-button:hover {
  background-color: #999;
}

.success-message {
  color: green;
  font-size: 14px;
  margin-top: 10px;
}

.error-message {
  color: red;
  font-size: 14px;
  margin-top: 10px;
}
</style>

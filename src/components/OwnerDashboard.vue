<template>
  <div id="owner-dashboard">
    <h1>Welkom, eigenaar!</h1>
    <p>Dit is je dashboard waar je camping spots kunt beheren.</p>
    <button @click="showAddForm = true" class="add-button">Campingspot toevoegen</button>

    <!-- Lijst van campingspots -->
    <div class="camping-list">
      <h2>Mijn Campingspots</h2>
      <div class="spacer"></div>
      <div class="camping-card" v-for="spot in campingSpots" :key="spot.spot_id">
        <div class="camping-details">
          <h3>{{ spot.name }}</h3>
          <p><strong>Omschrijving:</strong> {{ spot.description }}</p>
          <p><strong>Locatie:</strong> {{ spot.location }}</p>
          <p><strong>Prijs per nacht:</strong> €{{ spot.price }}</p>
        </div>
        <button class="delete-button" @click="deleteCampingSpot(spot.spot_id)">
          🗑️
        </button>
      </div>
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
      showAddForm: false,
      campingSpots: [],
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
    this.fetchCampingSpots(); // Haal campingspots op bij het laden van de pagina
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
          name: this.newCampingSpot.name || null, //Lege waarden omzetten naar null, voorkomt fouten in db
          location: this.newCampingSpot.location || null,
          description: this.newCampingSpot.description || null,
          price: this.newCampingSpot.price || null,
        };

        const response = await axios.post("http://localhost:3000/api/campingspots", campingSpotData);
        if (response.data.success) {
          this.successMessage = `Campingspot "${this.newCampingSpot.name}" is succesvol toegevoegd!`;
          this.fetchCampingSpots();
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
          this.fetchCampingSpots();
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


<style scoped>
/* Specifieke styling voor de modal in OwnerDashboard */
#owner-dashboard .modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000; /* Zorg ervoor dat het boven andere content wordt weergegeven */
}

#owner-dashboard .modal-content {
  background: white;
  padding: 20px;
  border-radius: 8px;
  width: 400px;
  max-width: 90%;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  font-family: Arial, sans-serif;
}

#owner-dashboard form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

#owner-dashboard input,
#owner-dashboard textarea {
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: 100%;
  box-sizing: border-box;
}

#owner-dashboard textarea {
  resize: vertical;
  height: 100px;
}

#owner-dashboard .save-button {
  background-color: #5c0a5c;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 10px;
  cursor: pointer;
  font-size: 16px;
}

#owner-dashboard .save-button:hover {
  background-color: #901090;
}

#owner-dashboard .cancel-button {
  background-color: #ccc;
  color: black;
  border: none;
  border-radius: 5px;
  padding: 10px;
  cursor: pointer;
  font-size: 16px;
}

#owner-dashboard .cancel-button:hover {
  background-color: #999;
}

#owner-dashboard .success-message {
  color: green;
  font-size: 14px;
  margin-top: 10px;
}

#owner-dashboard .error-message {
  color: red;
  font-size: 14px;
  margin-top: 10px;
}

.camping-list {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  margin-top: 20px;
}

h2 {
  margin-bottom: 30px; /* Ruimte onder de "Mijn Campingspots"-titel */
  font-size: 24px; /* Optioneel: Vergroot het lettertype voor visuele balans */
}

.camping-card {
  background-color: #fff;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 250px;
  padding: 20px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  font-family: Arial, sans-serif;
}

.camping-details h3 {
  margin: 0 0 10px;
  font-size: 18px;
  color: #5c0a5c;
}

.camping-details p {
  margin: 5px 0;
  font-size: 14px;
  color: #333;
}

.delete-button {
  background-color: transparent;
  border: none;
  color: #5c0a5c;
  font-size: 24px;
  cursor: pointer;
  transition: color 0.3s;
}

.spacer {
 /* Ruimte tussen de titel en de lijst */
  width: 100%;
}

.delete-button:hover {
  color: #901090;
}

</style>



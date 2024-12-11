<template>
  <div id="camping-spots-page">
    <h2>Camping Spots</h2>
    <div class="camping-spots-container">
      <div class="camping-spot" v-for="spot in campingSpots" :key="spot.spot_id">
        <h3>{{ spot.name }}</h3>
        <p>{{ spot.description }}</p>
        <p><strong>Locatie:</strong> {{ spot.location }}</p>
        <p><strong>Prijs per nacht:</strong> €{{ spot.price }}</p>
        <button class="book-button" @click="bookSpot(spot.spot_id, spot.name)">Boeken</button>
      </div>
    </div>
    <p v-if="campingSpots.length === 0" class="error-message">Geen campingspots gevonden.</p>

    <!-- Pop-up melding -->
    <div v-if="showPopup" class="popup">
      <div class="popup-content">
        <p>{{ popupMessage }}</p>
        <button @click="closePopup">Sluiten</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      campingSpots: [],
      showPopup: false,
      popupMessage: '',
    };
  },
  mounted() {
    fetch('http://localhost:3000/api/campingspots')
      .then((res) => res.json())
      .then((data) => {
        this.campingSpots = data.campingSpots || [];
      })
      .catch((err) => {
        console.error('Fout bij ophalen campingplekken:', err);
      });
  },
  methods: {
    async bookSpot(spotId, spotName) {
      const userId = localStorage.getItem('userId'); // Haal de gebruiker-ID op
      if (!userId) {
        this.showPopup = true;
        this.popupMessage = 'Je moet ingelogd zijn om te boeken.';
        return;
      }

      try {
        const response = await fetch('http://localhost:3000/api/bookings', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ userId, spotId }),
        });

        const data = await response.json();
        if (data.success) {
          this.showPopup = true;
          this.popupMessage = `Campingspot "${spotName}" is succesvol geboekt!`;
        } else {
          this.showPopup = true;
          this.popupMessage = `Fout: ${data.message}`;
        }
      } catch (err) {
        console.error('Fout bij het boeken van de campingspot:', err);
        this.showPopup = true;
        this.popupMessage = 'Er is een fout opgetreden. Probeer het opnieuw.';
      }
    },
    closePopup() {
      this.showPopup = false;
      this.popupMessage = '';
    },
  },
};
</script>



<style scoped>
#camping-spots-page {
  padding: 20px;
  font-family: Arial, sans-serif;
}

.camping-spots-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.camping-spot {
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 15px;
  background-color: #f9f9f9;
  width: 250px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.camping-spot h3 {
  margin: 0 0 10px;
  font-size: 18px;
}

.camping-spot p {
  margin: 5px 0;
  font-size: 14px;
  color: #555;
}

.book-button {
  margin-top: 10px;
  padding: 10px 20px;
  font-size: 16px;
  background-color: #5c0a5c;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.book-button:hover {
  background-color: #901090;
}

/* Pop-up styling */
.popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.popup-content {
  background: #5c0a5c; /* Paarse achtergrond */
  padding: 20px;
  border-radius: 10px;
  text-align: center;
  font-size: 18px;
  color: white !important; /* Zorg dat de tekst wit blijft */
  font-weight: bold; /* Tekst in het vet */
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.popup-content p {
  margin: 0;
  color: white !important; /* Extra specifiek om wit te forceren */
}
</style>






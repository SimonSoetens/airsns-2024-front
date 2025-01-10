<template>
  <div id="login-page">
    <!-- Navigatieknoppen -->
    <nav>
      <button @click="currentView = 'login'">Inloggen</button>
      <button @click="currentView = 'register'">Registreren</button>
    </nav>

    <!-- Weergave voor inloggen -->
    <div v-if="currentView === 'login'">
      <h2>Inloggen</h2>
      <form @submit.prevent="login" class="form-style"> <!-- @submit.prevent voorkomt standaardgedrag van formulier -->
        <label for="email">E-mail:</label>
        <input id="email" v-model="email" type="email" placeholder="E-mail" required />

        <label for="password">Wachtwoord:</label>
        <input id="password" v-model="password" type="password" placeholder="Wachtwoord" required />

        <button type="submit">Inloggen</button>
      </form>

      <!-- Foutmelding -->
      <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>

      <!-- Succesbericht voor registreren -->
      <p v-if="registrationSuccess" class="success-message">
        Registreren succesvol, U kunt nu inloggen.
      </p>
    </div>

    <!-- Weergave voor registreren -->
    <div v-if="currentView === 'register'">
      <h2>Registreren</h2>
      <form @submit.prevent="register" class="form-style">
        <label for="name">Naam:</label>
        <input id="name" v-model="name" type="text" placeholder="Naam" required />

        <label for="firstname">Voornaam:</label>
        <input id="firstname" v-model="firstname" type="text" placeholder="Voornaam" required />

        <label for="email-register">E-mail:</label>
        <input id="email-register" v-model="email" type="email" placeholder="E-mail" required />

        <label for="phone">Telefoonnummer:</label>
        <input id="phone" v-model="phone" type="text" placeholder="Telefoonnummer" required />

        <label for="date_of_birth">Geboortedatum:</label>
        <input id="date_of_birth" v-model="date_of_birth" type="date" required />

        <label for="country">Land:</label>
        <input
          id="country-search"
          v-model="searchQuery"
          type="text"
          placeholder="Zoek land..."
          @input="filterCountries"
        />
        <select id="country" v-model="country" required>
          <option v-for="country in filteredCountries" :key="country" :value="country">
            {{ country }}
          </option>
        </select>

        <label for="password-register">Wachtwoord:</label>
        <input id="password-register" v-model="password" type="password" placeholder="Wachtwoord" required />

        <label for="confirmPassword">Herhaal Wachtwoord:</label>
        <input id="confirmPassword" v-model="confirmPassword" type="password" placeholder="Herhaal Wachtwoord" required />

        <button type="submit">Registreren</button>
      </form>
    </div>

    <!-- Welcome Modal -->
    <WelcomeModal :show="showWelcomeModal" @close="showWelcomeModal = false" />
  </div>
</template>

<script>
import axios from "axios";
import WelcomeModal from "@/components/WelcomeModal.vue";

export default {
  components: {
    WelcomeModal,
  },
  data() {
    return {
      currentView: "login", // Startweergave
      email: "",
      password: "",
      name: "",
      firstname: "",
      phone: "",
      date_of_birth: "",
      country: "",
      confirmPassword: "",
      searchQuery: "",
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
      filteredCountries: [],
      showWelcomeModal: false, 
      registrationSuccess: false, 
      errorMessage: "", 
    };
  },
  mounted() {
    this.filteredCountries = this.countries; // Standaard alle landen tonen
  },
  methods: {
    filterCountries() {
      const query = this.searchQuery.toLowerCase();
      this.filteredCountries = this.countries.filter((country) =>
        country.toLowerCase().includes(query)
      );
    },
    async login() {
      try {
        const response = await axios.post("http://localhost:3000/api/login", {
          email: this.email,
          password: this.password,
        });

        if (response.data.success) {
          if (response.data.isOwner) {
            this.$emit("setOwnerMode", true); // Emit event naar parent
          } else {
            // Normale gebruiker inloggen
            localStorage.setItem("userId", response.data.userId);
            this.showWelcomeModal = true;
          }
        } else {
          this.errorMessage = response.data.message || "Inloggen mislukt. Controleer je gegevens.";
        }
      } catch (err) {
        console.error("Fout bij inloggen:", err.response ? err.response.data : err);
        this.errorMessage = "Er is een fout opgetreden bij het inloggen. Probeer het opnieuw.";
      }
    },
    async register() {
      try {
        const response = await axios.post("http://localhost:3000/api/register", {
          name: this.name,
          firstname: this.firstname,
          email: this.email,
          phone: this.phone,
          date_of_birth: this.date_of_birth,
          country: this.country,
          password: this.password,
        });
        if (response.data.success) {
          this.registrationSuccess = true; // Toon succesbericht
          this.currentView = "login"; // Schakel over naar de loginweergave
        } else {
          this.errorMessage = "Registreren mislukt: " + response.data.message;
        }
      } catch (error) {
        console.error("Fout bij registreren:", error);
        this.errorMessage = "Er is een fout opgetreden. Probeer het opnieuw.";
      }
    },
  },
};
</script>

<style>
/* Styling om alles onder elkaar te houden */
.form-style {
  display: flex;
  flex-direction: column;
  gap: 10px;
  max-width: 400px; /* Zorgt voor consistentie met de profielpagina */
  margin: 0 auto;
}

label {
  font-weight: bold;
}

input,
select {
  padding: 8px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  max-width: 400px; /* Zorgt voor consistentie met de profielpagina */
  width: 100%; /* Vul de beschikbare ruimte binnen de container */
  box-sizing: border-box; /* Houd padding mee in de breedteberekening */
}

/* Specifieke aanpassing voor het landen selectievak bij registratie */
select {
  max-width: 400px; /* Zelfde breedte als andere velden */
  width: 100%;
}

button {
  margin-top: 15px;
  padding: 10px;
  font-size: 16px;
  background-color: #5c0a5c;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  text-align: center; 
  box-sizing: border-box; /* Zorgt dat padding wordt meegerekend */
}

button[type="submit"] {
  max-width: 400px; 
  width: 100%; 
}

button:hover {
  background-color: #901090;
}

.success-message {
  color: #28a745;
  margin-top: 10px;
  font-size: 14px;
  text-align: center;
}

.error-message {
  color: red;
  margin-top: 10px;
  font-size: 14px;
  text-align: center;
}
</style>

<template>
  <div id="login-page">
    <h1>Login of Registreren</h1>

    <!-- Navigatieknoppen -->
    <nav>
      <button @click="currentView = 'login'">Inloggen</button>
      <button @click="currentView = 'register'">Registreren</button>
    </nav>

    <!-- Weergave voor inloggen -->
    <div v-if="currentView === 'login'">
      <h2>Inloggen</h2>
      <form @submit.prevent="login" class="form-style">
        <label for="email">E-mail:</label>
        <input id="email" v-model="email" type="email" placeholder="E-mail" required />

        <label for="password">Wachtwoord:</label>
        <input id="password" v-model="password" type="password" placeholder="Wachtwoord" required />

        <button type="submit">Inloggen</button>
      </form>
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
      filteredCountries: [],
      showWelcomeModal: false, // Voor het modaal
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
          alert("Registreren succesvol! Log nu in.");
          this.currentView = "login"; // Schakel over naar de loginweergave
        } else {
          alert("Registreren mislukt: " + response.data.message);
        }
      } catch (error) {
        console.error("Fout bij registreren:", error);
        alert("Er is een fout opgetreden. Probeer het opnieuw.");
      }
    },
    async login() {
      try {
        const response = await axios.post("http://localhost:3000/api/login", {
          email: this.email,
          password: this.password,
        });
        if (response.data.success) {
          localStorage.setItem("userId", response.data.userId); // Sla userId op
          this.showWelcomeModal = true; // Toon het modaal
        } else {
          alert(response.data.message || "Inloggen mislukt. Controleer je gegevens.");
        }
        console.log("Modal status:", this.showWelcomeModal);
      } catch (err) {
        console.error("Fout bij inloggen:", err.response ? err.response.data : err);
        alert("Er is een fout opgetreden bij het inloggen. Probeer het opnieuw.");
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
  max-width: 400px;
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
}

button:hover {
  background-color: #901090;
}
</style>

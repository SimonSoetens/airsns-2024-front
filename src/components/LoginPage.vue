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
      <form @submit.prevent="login">
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
      <form @submit.prevent="register" class="register-form">
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
        <input id="country" v-model="country" type="text" placeholder="Land" required />

        <label for="password-register">Wachtwoord:</label>
        <input id="password-register" v-model="password" type="password" placeholder="Wachtwoord" required />

        <label for="confirmPassword">Herhaal Wachtwoord:</label>
        <input id="confirmPassword" v-model="confirmPassword" type="password" placeholder="Herhaal Wachtwoord" required />

        <button type="submit">Registreren</button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
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
    };
  },
  methods: {
    login() {
      // Stuur login-gegevens naar de server
      const loginData = { email: this.email, password: this.password };
      fetch("http://localhost:3000/api/login", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(loginData),
      })
        .then((res) => res.json())
        .then((data) => {
          if (data.success) {
            alert("Succesvol ingelogd!");
          } else {
            alert("Fout bij inloggen: " + data.error);
          }
        })
        .catch((err) => alert("Serverfout: " + err.message));
    },
    register() {
      // Controleer of wachtwoorden overeenkomen
      if (this.password !== this.confirmPassword) {
        alert("Wachtwoorden komen niet overeen!");
        return;
      }

      // Stuur registratiegegevens naar de server
      const registerData = {
        name: this.name,
        firstname: this.firstname,
        email: this.email,
        phone: this.phone,
        date_of_birth: this.date_of_birth,
        country: this.country,
        password: this.password,
      };
      fetch("http://localhost:3000/api/register", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(registerData),
      })
        .then((res) => res.json())
        .then((data) => {
          if (data.success) {
            alert("Succesvol geregistreerd!");
            this.currentView = "login"; // Schakel naar login na registratie
          } else {
            alert("Fout bij registreren: " + data.error);
          }
        })
        .catch((err) => alert("Serverfout: " + err.message));
    },
  },
};
</script>

<style>
/* Styling voor de knoppen */
nav button {
  margin: 5px;
  padding: 10px 20px;
  cursor: pointer;
}

/* Styling voor het formulier */
form {
  display: flex;
  flex-direction: column;
  max-width: 400px;
  margin: 0 auto;
}

form label {
  margin-top: 10px;
  font-weight: bold;
}

form input {
  margin-top: 5px;
  padding: 8px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

form button {
  margin-top: 15px;
  padding: 10px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

form button:hover {
  background-color: #45a049;
}
</style>

<template>
  <div>
    <h2 v-if="isLogin">Login</h2>
    <h2 v-else>Account aanmaken</h2>

    <nav>
      <button @click="isLogin = true">Login</button>
      <button @click="isLogin = false">Registreren</button>
    </nav>

    <!-- Login Form -->
    <form v-if="isLogin" @submit.prevent="login">
      <div>
        <input v-model="email" type="email" placeholder="E-mail" required />
      </div>
      <div>
        <input v-model="password" type="password" placeholder="Wachtwoord" required />
      </div>
      <button type="submit">Inloggen</button>
    </form>

    <!-- Registration Form -->
    <form v-else @submit.prevent="register">
      <div>
        <label>Naam:</label>
        <input v-model="name" placeholder="Naam" required />
      </div>
      <div>
        <label>Voornaam:</label>
        <input v-model="firstname" placeholder="Voornaam" required />
      </div>
      <div>
        <label>E-mail:</label>
        <input v-model="email" type="email" placeholder="E-mail" required />
      </div>
      <div>
        <label>Telefoonnummer:</label>
        <input v-model="phone" type="tel" placeholder="Telefoonnummer" required />
      </div>
      <div>
        <label>Geboortedatum:</label>
        <input v-model="birth_date" type="date" placeholder="Geboortedatum" required />
      </div>
      <div>
        <label>Land:</label>
        <div>
          <input v-model="countrySearch" placeholder="Zoek je land..." />
          <select v-model="country" required>
            <option v-for="option in filteredCountries" :key="option" :value="option">
              {{ option }}
            </option>
          </select>
        </div>
      </div>
      <button type="submit">Registreren</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // Login data
      isLogin: true,
      email: '',
      password: '',

      // Registration data
      name: '',
      firstname: '',
      phone: '',
      birth_date: '',
      country: '', // Geselecteerd land
      countrySearch: '', // Zoekveld voor landen

      // Lijst van landen
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
        "Azerbaijan",
        "Bahamas",
        "Bahrain",
        "Bangladesh",
        "Barbados",
        "Belarus",
        "Belgium",
        "Belize",
        "Benin",
        "Bhutan",
        "Bolivia",
        "Bosnia and Herzegovina",
        "Botswana",
        "Brazil",
        "Brunei",
        "Bulgaria",
        "Burkina Faso",
        "Burundi",
        "Cabo Verde",
        "Cambodia",
        "Cameroon",
        "Canada",
        "Central African Republic",
        "Chad",
        "Chile",
        "China",
        "Colombia",
        "Comoros",
        "Congo (Congo-Brazzaville)",
        "Costa Rica",
        "Croatia",
        "Cuba",
        "Cyprus",
        "Czechia",
        "Denmark",
        "Djibouti",
        "Dominica",
        "Dominican Republic",
        "Ecuador",
        "Egypt",
        "El Salvador",
        "Equatorial Guinea",
        "Eritrea",
        "Estonia",
        "Eswatini",
        "Ethiopia",
        "Fiji",
        "Finland",
        "France",
        "Gabon",
        "Gambia",
        "Georgia",
        "Germany",
        "Ghana",
        "Greece",
        "Grenada",
        "Guatemala",
        "Guinea",
        "Guinea-Bissau",
        "Guyana",
        "Haiti",
        "Holy See",
        "Honduras",
        "Hungary",
        "Iceland",
        "India",
        "Indonesia",
        "Iran",
        "Iraq",
        "Ireland",
        "Israel",
        "Italy",
        "Jamaica",
        "Japan",
        "Jordan",
        "Kazakhstan",
        "Kenya",
        "Kiribati",
        "Korea (North)",
        "Korea (South)",
        "Kosovo",
        "Kuwait",
        "Kyrgyzstan",
        "Laos",
        "Latvia",
        "Lebanon",
        "Lesotho",
        "Liberia",
        "Libya",
        "Liechtenstein",
        "Lithuania",
        "Luxembourg",
        "Madagascar",
        "Malawi",
        "Malaysia",
        "Maldives",
        "Mali",
        "Malta",
        "Marshall Islands",
        "Mauritania",
        "Mauritius",
        "Mexico",
        "Micronesia",
        "Moldova",
        "Monaco",
        "Mongolia",
        "Montenegro",
        "Morocco",
        "Mozambique",
        "Myanmar",
        "Namibia",
        "Nauru",
        "Nepal",
        "Netherlands",
        "New Zealand",
        "Nicaragua",
        "Niger",
        "Nigeria",
        "North Macedonia",
        "Norway",
        "Oman",
        "Pakistan",
        "Palau",
        "Palestine State",
        "Panama",
        "Papua New Guinea",
        "Paraguay",
        "Peru",
        "Philippines",
        "Poland",
        "Portugal",
        "Qatar",
        "Romania",
        "Russia",
        "Rwanda",
        "Saint Kitts and Nevis",
        "Saint Lucia",
        "Saint Vincent and the Grenadines",
        "Samoa",
        "San Marino",
        "Sao Tome and Principe",
        "Saudi Arabia",
        "Senegal",
        "Serbia",
        "Seychelles",
        "Sierra Leone",
        "Singapore",
        "Slovakia",
        "Slovenia",
        "Solomon Islands",
        "Somalia",
        "South Africa",
        "South Sudan",
        "Spain",
        "Sri Lanka",
        "Sudan",
        "Suriname",
        "Sweden",
        "Switzerland",
        "Syria",
        "Tajikistan",
        "Tanzania",
        "Thailand",
        "Timor-Leste",
        "Togo",
        "Tonga",
        "Trinidad and Tobago",
        "Tunisia",
        "Turkey",
        "Turkmenistan",
        "Tuvalu",
        "Uganda",
        "Ukraine",
        "United Arab Emirates",
        "United Kingdom",
        "United States of America",
        "Uruguay",
        "Uzbekistan",
        "Vanuatu",
        "Venezuela",
        "Vietnam",
        "Yemen",
        "Zambia",
        "Zimbabwe",
      ],
    };
  },
  computed: {
    filteredCountries() {
      if (!this.countrySearch) return this.countries;
      return this.countries.filter((country) =>
        country.toLowerCase().includes(this.countrySearch.toLowerCase())
      );
    },
  },
  methods: {
    register() {
      fetch('http://localhost:3000/api/register', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          name: this.name,
          firstname: this.firstname,
          email: this.email,
          phone: this.phone,
          birth_date: this.birth_date,
          country: this.country,
        }),
      })
        .then((res) => res.json())
        .then((data) => {
          if (data.success) {
            alert('Succesvol geregistreerd! Log nu in.');
            this.isLogin = true;
          } else {
            alert('Registratie mislukt.');
          }
        })
        .catch((err) => {
          console.error('Fout bij registreren:', err);
        });
    },
  },
};
</script>

<template>
  <div class="modal-overlay" v-if="show">
    <div class="modal">
      <div class="logo-container">
        <img src="@/assets/Logo.png" alt="AirSnS Logo" class="logo" /> <!--alt als afbeelding niet geladen kan worden of voor slechtzienden-->
      </div>
      <h2>Welkom bij AirSnS</h2>
      <p>Ontdek de mooiste kampeerplekken en bijzondere ervaringen in heel België.</p>
      <button @click="redirectToHome">Doorgaan</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    show: {
      type: Boolean,
      default: false,
    },
  },
  methods: {
    redirectToHome() {
      this.$emit("close");
      window.location.href = "/"; // Verwijst direct naar de HomePage
    },
  },
};
</script>

<style>
.modal-overlay {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1000;
}

.modal {
  background: white;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
  max-width: 400px;
  width: 90%;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.logo-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 20px;
}

.logo {
  height: 100px;
}

h2 {
  margin-top: 10px;
  font-size: 18px;
  color: #333;
}

p {
  margin-top: 10px;
  font-size: 14px;
  color: #555;
}

button {
  background-color: #5c0a5c;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
  margin-top: 20px;
}

button:hover {
  background-color: #901090;
}
</style>

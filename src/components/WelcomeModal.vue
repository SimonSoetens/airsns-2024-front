<template>
  <div class="modal-overlay" v-if="show">
    <div class="modal">
      <button class="close-button" @click="closeModal">×</button>
      <img src="https://via.placeholder.com/100" alt="Logo" class="logo" />
      <h2>Welkom bij AirSnS</h2>
      <p>Ontdek de mooiste kampeerplekken en bijzondere ervaringen over de hele wereld.</p>
      <button @click="redirectToProfile">Doorgaan</button>
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
    closeModal() {
      this.$emit("close");
    },
    redirectToProfile() {
      window.location.href = "/profile";
    },
  },
};
</script>

<style>
.modal-overlay {
  display: flex; /* Zorg dat het zichtbaar is */
  justify-content: center;
  align-items: center;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 1000; /* Zorg dat het boven alles wordt weergegeven */
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

.close-button {
    position: absolute;
    top: 10px;
    right: 10px;
    background: none;
    border: none;
    font-size: 18px;
    cursor: pointer;
}

.logo {
    margin-bottom: 20px;
}

button {
    background-color: #000;
    color: #fff;
    border: none;
    padding: 10px 20px;
    border-radius: 5px;
    cursor: pointer;
    margin-top: 20px;
}

button:hover {
    background-color: #444;
}
</style>


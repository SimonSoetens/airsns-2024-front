<template>
  <div>
    <h2>Camping Spots</h2>
    <ul>
      <li v-for="spot in campingSpots" :key="spot.spot_id">
        <h3>{{ spot.name }}</h3>
        <p>{{ spot.description }}</p>
        <p>Prijs: €{{ spot.price }}</p>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      campingSpots: [],
    };
  },
  mounted() {
    fetch('http://localhost:3000/api/campingspots')
      .then((res) => res.json())
      .then((data) => {
        this.campingSpots = data;
      })
      .catch((err) => {
        console.error('Fout bij ophalen campingplekken:', err);
      });
  },
};
</script>

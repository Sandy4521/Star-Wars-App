<template>
  <div>
    <h2>Planet Details {{ planet.name }}</h2>
    <h3>Residents:</h3>
    <ul>
      <li v-for="resident in planet.residents" :key="resident">
        {{ resident }}
      </li>
    </ul>
  </div>
</template>
  
<script>
import axios from 'axios';

export default {
  data() {
    return {
      planet: {},
    };
  },
  async mounted() {
    const planetId = this.$route.params.id;
    try {
      const response = await axios.get(`https://swapi.dev/api/planets/${planetId}/`);
      this.planet = response.data;
    } catch (error) {
      console.error('Error fetching planet details:', error);
    }
  },
};
</script>
  










  <!-- Code by Sandy(Sandor) -->
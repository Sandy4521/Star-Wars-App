<template>
  <div class="container">
    <h2>List of Planets</h2>
    <div v-if="isLoading">
      <p>Loading...</p>
    </div>
    <div v-else-if="isError">
      <p>An error occurred while fetching data.</p>
    </div>
    <ul v-else>
      <li v-for="planet in planets" :key="planet.name" class="planet-item">
        <div class="planet-name">{{ planet.name }}</div>
        <button @click="fetchResidents(planet)" class="more-info-button" v-if="!planet.showResidents">
          More Info
        </button>
        <button @click="closeResidents(planet)" class="more-info-button" v-else>
          Close Info
        </button>
        <button @click="toggleFavorite(planet)" class="favorite-button" v-if="!isFavorite(planet)">
          Add to Favorites
        </button>
        <button @click="toggleFavorite(planet)" class="favorite-button" v-else>
          Remove Favorite
        </button>
        <div v-if="planet.showResidents" class="resident-list">
          <h3>Residents:</h3>
          <ul>
            <li v-for="resident in planet.residentsInfo" :key="resident.name">
              {{ resident.name }}
            </li>
          </ul>
        </div>
      </li>
    </ul>

    <h2>Favorite Planets</h2>
    <ul>
      <li v-for="favorite in favoritePlanets" :key="favorite.name" class="favorite-planet">
        {{ favorite.name }}
      </li>
    </ul>
  </div>
</template>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.planet-item {
  border: 1px solid #ddd;
  border-radius: 5px;
  margin-bottom: 10px;
  padding: 10px;
  display: flex;
  align-items: center;
}

.planet-name {
  flex: 1;
}

.more-info-button {
  background-color: #3498db;
  color: white;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
  border-radius: 3px;
  margin-left: 10px;
}

.favorite-button {
  background-color: #f39c12;
  color: white;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
  border-radius: 3px;
  margin-left: 10px;
}

.resident-list {
  border: 1px solid #ccc;
  padding: 10px;
  border-radius: 5px;
  background-color: #f5f5f5;
  margin-top: 10px;
}

.loading-spinner {
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-left: 4px solid #3498db;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  animation: spin 1s linear infinite;
  margin: 0 auto;
}

.favorite-planet {
  background-color: #2ecc71;
  color: white;
  padding: 5px 10px;
  border-radius: 3px;
  margin-bottom: 5px;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}
</style>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      planets: [],
      isLoading: false,
      isError: false,
      favoritePlanets: [],
    };
  },
  async mounted() {
    try {
      // Display loading message while fetching data
      this.isLoading = true;
      const response = await axios.get('https://swapi.dev/api/planets/');
      // Update the list of planets with additional properties
      this.planets = response.data.results.map(planet => ({
        ...planet,
        showResidents: false,
        residentsInfo: [],
      }));
      // Load favorite planets from local storage
      this.loadFavoritePlanetsFromLocalStorage();
    } catch (error) {
      // Display error message if fetching planets fails
      console.error('Error fetching planets:', error);
      this.isError = true;
    } finally {
      // Hide loading message
      this.isLoading = false;
    }
  },
  methods: {
    async fetchResidents(planet) {
      // Fetch and display residents information
      if (!planet.residentsInfo.length) {
        const residentUrls = planet.residents;
        await Promise.all(
          residentUrls.map(async residentUrl => {
            try {
              const response = await axios.get(residentUrl);
              planet.residentsInfo.push(response.data);
            } catch (error) {
              console.error('Error fetching resident details:', error);
            }
          })
        );
      }
      planet.showResidents = true;
    },
    closeResidents(planet) {
      // Hide residents information
      planet.showResidents = false;
    },
    toggleFavorite(planet) {
      // Toggle the favorite status of a planet
      if (!this.isFavorite(planet)) {
        this.favoritePlanets.push(planet);
      } else {
        this.favoritePlanets = this.favoritePlanets.filter(favorite => favorite.name !== planet.name);
      }
      // Update local storage with updated favorite planets
      this.updateLocalStorage();
    },
    isFavorite(planet) {
      // Check if a planet is in the favorite planets list
      return this.favoritePlanets.some(favorite => favorite.name === planet.name);
    },
    updateLocalStorage() {
      // Update local storage with the favorite planets list
      localStorage.setItem('favoritePlanets', JSON.stringify(this.favoritePlanets));
    },
    loadFavoritePlanetsFromLocalStorage() {
      // Load favorite planets from local storage
      const storedFavorites = localStorage.getItem('favoritePlanets');
      if (storedFavorites) {
        this.favoritePlanets = JSON.parse(storedFavorites);
      }
    },
  },
};
</script>









<!-- Code by Sandy(Sandor) -->


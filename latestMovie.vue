<template>
  <div>
    <h1>API Search</h1>
    <input type="text" v-model="searchQuery">
    <button @click="search">Search</button>
    <div v-if="isLoading">Loading...</div>
    <div v-else class="results-container">
      <div v-for="result in results" :key="result.imdbID" class="result-item">
        <img :src="result.Poster" alt="Poster for {{ result.Title }}" />
        <h3>{{ result.Title }}</h3>
        <p>{{ result.Plot }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue';
import axios from 'axios';

const API_KEY = 'ad6d4442';
const searchQuery = ref('');
const results = reactive([]);
const isLoading = ref(false);

const search = () => {
  if (searchQuery.value.trim() === '') {
    results.length = 0;
    return;
  }

  isLoading.value = true;

  axios.get('http://www.omdbapi.com/', {
    params: {
      apikey: API_KEY,
      i: 'tt3896198',
      s: searchQuery.value,
    },
  })
    .then(response => {
      results.splice(0, results.length, ...(response.data.Search || []));
      isLoading.value = false;
    })
    .catch(error => {
      console.error('Error fetching data:', error);
      isLoading.value = false;
    });
};
</script>

<style>

.results-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}

.result-item {
  background-color: #f0f0f0;
  border: 1px solid #ddd;
  padding: 15px;
  margin: 10px;
  width: 300px;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.result-item img {
  width: 100%;
  height: 250px;
  object-fit: cover;
  border-radius: 5px 5px 0 0;
}

.result-item h3 {
  margin: 0;
  font-size: 1.2rem;
}

.result-item p {
  font-size: 0.9rem;
  margin-top: 10px;
}
</style>
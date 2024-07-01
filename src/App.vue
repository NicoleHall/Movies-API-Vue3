<template>
  <div class="app">
    <h1>Movies Featuring Tom Hanks</h1>
    <ul v-if="allTheMoviesOfOneActor.length > 0">
      <li v-for="movie in allTheMoviesOfOneActor" :key="movie.title">
        <h2>{{ movie.title }}</h2>
        <p><strong>Release Date:</strong> {{ movie.release_date }}</p>
        <p>{{ movie.overview }}</p>
      </li>
    </ul>
    <p v-else>No movies found.</p>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from 'vue';

interface Movie {
  title: string;
  release_date: string;
  overview: string;
}

export default defineComponent({
  name: 'App',
  setup() {
    const allTheMoviesOfOneActor = ref<Movie[]>([]);

    async function getAllMovies(url: string, api_key: string): Promise<Movie[]> {
      const response = await fetch(url + api_key);
      const json = await response.json();
      return json.results as Movie[];
    }

    async function loadMovies() {
      const url = 'https://api.themoviedb.org/3/search/movie?query=tom+hanks&api_key=';
      const api_key = process.env.VUE_APP_API_SECRET_KEY; 

      try {
        const movies = await getAllMovies(url, api_key);
        allTheMoviesOfOneActor.value = movies.filter(movie => movie.title && movie.release_date && movie.overview);
      } catch (error) {
        console.error('Error fetching movies:', error);
      }
    }

    onMounted(loadMovies);

    return { allTheMoviesOfOneActor };
  },
});
</script>

<style>
.app {
  font-family: 'Arial', sans-serif;
  margin: 20px;
}

h1 {
  color: #333;
}

h2 {
  color: #666;
}

p {
  font-size: 16px;
  color: #444;
}
</style>

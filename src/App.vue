<template>
  <div class="app">
    <input v-model="characterName" placeholder="Name of character + enter" @keyup.enter="loadMovies"/>
    <p>examples: Barbie, Indiana Jones, Harry Potter, Batman</p>
    <h1>Movies featuring {{ characterName }}</h1>
    <ul v-if="allTheMoviesOfOneCharacter.length > 0">
      <li v-for="movie in allTheMoviesOfOneCharacter" :key="movie.title">
        <h2>{{ movie.title }}</h2>
        <p><strong>Release Date:</strong> {{ movie.release_date }}</p>
        <p>{{ movie.overview }}</p>
      </li>
    </ul>
    <p v-else>No movies found.</p>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue';

interface Movie {
  title: string;
  release_date: string;
  overview: string;
}

export default defineComponent({
  name: 'App',
  setup() {
    const characterName = ref('');
    const allTheMoviesOfOneCharacter = ref<Movie[]>([]);

    async function getAllMovies(url: string, api_key: string): Promise<Movie[]> {
      const response = await fetch(url + api_key);
      const json = await response.json();
      return json.results as Movie[];
    }

    async function loadMovies() {
      const formattedcharacterName = characterName.value.replace(/\s+/g, ' ').trim().replace(/\s/g, '+');
      const url = `https://api.themoviedb.org/3/search/movie?query=${formattedcharacterName}&api_key=`;
      const api_key = process.env.VUE_APP_API_SECRET_KEY; 

      try {
        const movies = await getAllMovies(url, api_key);
        allTheMoviesOfOneCharacter.value = movies.filter(movie => movie.title && movie.release_date && movie.overview);//filter takes a callback function that is expressed as an arrow function. 
      } catch (error) {
        console.error('Error fetching movies:', error);
      }
    }

    const formattedcharacterName = computed(() => characterName.value.replace(/\s+/g, ' ').trim().replace(/\s/g, '+'));

    return { characterName, allTheMoviesOfOneCharacter, loadMovies, formattedcharacterName };
  },
});
</script>

<style>
.app {
  font-family: 'Arial', sans-serif;
  margin: 20px;
  background-color: #ADD8E6; 
  margin: 20px;
  padding: 20px;  
  background-color: #FFF8DC;  
  border: 10px solid #ADD8E6;  
  box-sizing: border-box;
}

h1 {
  color: hotpink;
}

h2 {
  color: #2E8B57;
}

p {
  font-size: 16px;
  color: #444;
}
</style>

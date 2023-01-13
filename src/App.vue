

<script setup>
import { ref, computed, onMounted } from "vue";

const query = ref('')
const my_anime = ref([])
const search_results = ref([])
const my_anime_asc = computed(() => {
  return my_anime.value.sort((a, b) => {
    return a.title.localeCompare(b.title)
  })
})
const searchAnime = () => {
  const url = `https://api.jikan.moe/v4/anime?q=${query.value}`
  fetch(url)
    .then(res => res.json())
    .then(res => {
      search_results.value = res.data
    })
}

const handleInput = e => {
  if (!e.target.value) {
    search_results.value = []
  }
}


const addAnime = anime => {
  search_results.value = []
  query.value = ''
  my_anime.value.push({
    id: anime.mal_id,
    title: anime.title,
    image: anime.images.jpg.image_url,
    total_episodes: anime.episodes,
    watched_episodes: 0
  })
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const increaseWatch = anime => {
  anime.watched_episodes ++
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const decreaseWatch = anime => {
  anime.watched_episodes --
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

onMounted (() => {
  my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || []
})




</script>

<template>
  <main>
    <h1>Anime Watch Tracker</h1>
    <form @submit.prevent="searchAnime">
      <input 
        type="text" 
        placeholder="find an anime..." 
        v-model="query" 
        @input="handleInput"
      >
      <button type="submit" class="button"> search </button>
    </form>
    <div class="results" v-if="search_results.length > 0">
      <div class="result" v-for="anime in search_results">
        <img :src="anime.images.jpg.image_url" alt="anime cover">
        <div class="details">
          <h3> {{ anime.title }} </h3>
          <p :title="anime.synopsis" v-if="anime.synopsis" >
            {{ anime.synopsis.slice(0,120) }}....
          </p>
          <span class="flex-1"></span>
          <button class="button" @click="addAnime(anime)">Add Anime</button>
        </div>
      </div>
    </div>
    <div class="myanime" v-if="my_anime.length > 0">
      <h2>My anime</h2>
      <div v-for="anime in my_anime_asc" class="anime" >
        <img :src="anime.image" alt="anime image">
        <h3>{{ anime.title }}</h3>
        <div class="flex-1">
          <span class="episodes">
            {{ anime.watched_episodes }} / {{ anime.total_episodes }}
          </span>
          <div class="anibtn">
            <button class="button2" v-if="anime.total_episodes !== anime.watched_episodes" @click="increaseWatch(anime)">+</button>
            <button class="button2" v-if="anime.watched_episodes > 0" @click="decreaseWatch(anime)">-</button>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Fira sans', 'sans-serif';
}
body {
  background: #ffebcc;
  
}
main {
  margin: 0 auto;
  max-width: 768px;
  padding: 1.5rem;
}
h1 {
  text-align: center;
  margin-bottom: 1.5rem;
}
form {
  display: flex;
  max-width: 480px;
  margin: 0 auto 1.5rem;
}
form input {
  appearance: none;
  outline: none;
  border: none;
  background: #fff;
  display: flex;
  color: #888;
  font-size: 1.125rem;
  padding: 0.5rem 1rem;
  width: 100%;
  border-radius: 2rem;
  margin-right: 5px;
}
.button {
  appearance: none;
  outline: none;
  border: none;
  cursor: pointer;
  display: block;
  padding: 0.5rem 1rem;
  background-image: linear-gradient(to right, rgb(192, 0, 0) 50%, rgb(90, 0, 0) 50%);
  background-size: 200%;
  color: #fff;
  font-size: 1.125rem;
  font-weight: 700;
  text-transform: uppercase;
  transition: 0.4s;
  border-radius: 2rem;
}
.anibtn {
  display: flex;
  flex-direction: row;
  align-items: center;
  /* width: 100px; */
}
.button2 {
  appearance: none;
  outline: none;
  border: none;
  cursor: pointer;
  display: block;
  background-image: linear-gradient(to right, rgb(192, 0, 0) 50%, rgb(90, 0, 0) 50%);
  background-size: 200%;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  color: #fff;
  font-size: 35px;
  font-weight: 700;
  text-transform: uppercase;
  transition: 0.4s;
  margin: 10px 5px;
}
.button:hover, .button2:hover {
  background-position: right ;
}
.results {
  border-radius: 2.2rem;
  box-shadow: 0 2px 4px rgba(0 0 0 0.1);
  max-height: 480px;
  overflow-y: scroll;
  margin-bottom: 1.5rem;
}
.results::-webkit-scrollbar {
  width: 10px;
  height: 10px;
}

.results::-webkit-scrollbar-thumb {
  background: rgba(90, 90, 90);
  border-radius: 2rem;
}

.results::-webkit-scrollbar-track {
  background: rgba(0, 0, 0, 0);
}
.result {
  background: #fff;
  display: flex;
  margin: 1rem;
  padding: 1rem;
  border-radius: 2.2rem;
  transition: 0.4s;
}
.results img {
  width: 100px;
  height: 100px;
  object-fit: cover;
  border-radius: 2.2rem;
  margin-right: 1rem;
}
.details {
  flex: 1 1 0%;
  display: flex;
  align-items: center;
  flex-direction: column;
}
.flex-1 {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-direction: row;
}
.details h3 {
  font-size: 0.875rem;
  line-height: 1.4;
  margin-bottom: 0.5rem;
}
.details .button {
  margin-left: auto;
}
.myanime h2 {
  color: #888;
  font-weight: 400;
  margin-bottom: 1.5rem;
  text-align: center;
}
.myanime .anime {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  margin-bottom: 1.5rem;
  background-color: #fff;
  padding: 1rem;
  border-radius: 2.5rem;
  box-shadow: 0px 2px 4px rgba(0 0 0 0.1);

}
.episodes {
  margin: auto 20px;
}
.anime img {
  width: 72px;
  height: 72px;
  object-fit: cover;
  border-radius: 2rem;
  margin-right: 1rem;
}
.anime h3 {
  color: #888;
  font-size: 1.125rem;
}
.anime .button:first-of-type {
  margin-right: 1rem;
}
.anime .button:last-of-type {
  margin-right: 0;
}


</style>

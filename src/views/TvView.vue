<script setup>

import { useGenreStore } from '@/stores/genre';
import { ref, onMounted } from 'vue';
import api from '@/plugins/axios'
import Loading from 'vue-loading-overlay';
import { useRouter } from 'vue-router';

const genres = ref([]);
const shows = ref([]);
const isLoading = ref(false);
const genreStore = useGenreStore();
const router = useRouter();

function getGenreName(id) {
    const genero = genres.value.find((genre) => genre.id === id);
    return genero.name;
}

onMounted(async () => {
    isLoading.value = true;
    await genreStore.getAllGenres('tv');
    isLoading.value = false;
});

const listTv = async (genreId) => {
    genreStore.setCurrentGenreId(genreId);
    isLoading.value = true;
    const response = await api.get('discover/tv', {
        params: {
            with_genres: genreId,
            language: 'pt-BR'
        }
    });
    shows.value = response.data.results;
    isLoading.value = false;
}

function openTv(showId) {
    router.push({ name: 'TvDetails', params: { showId } });
}

</script>

<template>
    <h1>Programas de TV</h1>
    <ul class="genre-list">
        <li v-for="genre in genreStore.genres" :key="genre.id" @click="listTv(genre.id)" class="genre-item"
            :class="{ active: genre.id === genreStore.currentGenreId }"> {{ genre.name }} </li>
    </ul>

    <hr />

    <loading v-model:active="isLoading" is-full-page />
    <div class="tv-list">
        <div v-for="show in shows" :key="show.id" class="tv-card">
            <img :src="`https://image.tmdb.org/t/p/w500${show.poster_path}`" :alt="show.name"
                @click="openTv(show.id)" />
            <div class="tv-details">
                <p class="tv-title">{{ show.name }}</p>
                <p class="tv-realese-date">{{ show.first_air_date }}</p>
                <p class="tv-genres">
                    <span v-for="genre_id in show.genre_ids" :key="genre_id" @click="listTv(genre_id)"
                        :class="{ active: genre_id === genreStore.currentGenreId }">
                        {{ genreStore.getGenreName(genre_id) }}
                    </span>
                </p>
            </div>
        </div>
    </div>
</template>

<style scoped>
hr {
    color: #dfdfdf89;
    margin: 2rem 0 0 0;
}

.tv-list {
    justify-content: center;
    display: flex;
    flex-wrap: wrap;
    gap: 2rem;
    padding: 2rem;
    margin: 1.5rem 6rem 0 6rem;
    box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.237);
    background-color: rgba(0, 0, 0, 0.011);
    border-radius: 0.5rem;
}

.tv-card {
    background-color: white;
    width: 15rem;
    height: 35rem;
    border-radius: 0.5rem;
    overflow: hidden;
    box-shadow: 0 0 0.5rem rgb(219, 219, 219);
     transition: 0.7s;

}

.tv-card:hover {
    scale: 1.03;
    transition: 0.5s;
}


.tv-card img {
    width: 95%;
    height: 20rem;
    margin: 0.5vw 0 0 0;
    transform: translateX(2.5%);
    border-radius: 0.5rem;
    box-shadow: 0 0 0.5rem rgb(165, 165, 165);
}

.tv-details {
    font-size: 1.2rem;
    text-align: center;
    margin: 0.5vw 0.1rem 0 0.1rem;
}

.tv-genres {
    margin: 1rem 0.5vw 0 0.5vw;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    align-items: flex-start;
    justify-content: center;
    gap: 0.2rem;
}

.tv-genres span {
    background-color: #748708;
    border-radius: 0.5rem;
    padding: 0.2rem 0.5rem;
    color: #fff;
    font-size: 0.8rem;
    font-weight: bold;
}

.tv-genres span:hover {
    cursor: pointer;
    background-color: #455a08;
    box-shadow: 0 0 0.5rem #748708;
}

.active {
    background-color: #67b086;
    font-weight: bolder;
}

.tv-genres span.active {
    background-color: #abc322;
    color: #000;
    font-weight: bolder;
}


.genre-list {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    gap: 2rem;
    list-style: none;
    padding: 0;
}

.genre-item {
    background-color: #616543;
    border-radius: 1rem;
    padding: 0.5rem 1rem;
    align-self: center;
    color: #fff;
    display: flex;
    justify-content: center;
}

.genre-item:hover {
    cursor: pointer;
    background-color: #7d8a2e;
    box-shadow: 0 0 0.5rem #5d6424;
}
</style>
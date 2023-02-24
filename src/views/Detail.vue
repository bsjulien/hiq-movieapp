<template>
  <div class="detail">
    <h2>{{ movie.Title }}</h2>
    <p><strong>Runtime:</strong> {{ movie.Runtime }}</p>
    <p><strong>Genre:</strong> {{ movie.Genre }}</p>
    <p><strong>Director:</strong> {{ movie.Director }}</p>
    <p><strong>Actors:</strong> {{ movie.Actors }}</p>

    <p><strong>Year: </strong>{{ movie.Year }}</p>
    <img :src="movie.Poster" alt="Movie Poster" class="movie-poster" />
    <p><strong>Plot:</strong></p>
    <p>{{ movie.Plot }}</p>
  </div>
  <div v-if="loading" class="loading"></div>
</template>

<script>
import { ref, onBeforeMount } from "vue";
import { useRoute } from "vue-router";
import axios from "axios";

export default {
  setup() {
    const movie = ref({});
    const route = useRoute();
    const loading = ref(false);

    onBeforeMount(() => {
      loading.value = true;
      axios
        .get(
          `//www.omdbapi.com/?apikey=${process.env.VUE_APP_API_KEY}&i=${route.params.id}&plot=full`
        )
        .then((response) => {
          movie.value = response.data;
          loading.value = false;

          console.log(response.data);
        })
        .catch((error) => {
          console.log(error);
          loading.value = false;
        });
    });

    return {
      movie,
      loading,
    };
  },
};
</script>

<style lang="scss">
.detail {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  padding: 2rem;
  font-family: Arial, sans-serif;

  .movie-info {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-bottom: 2rem;

    .movie-title {
      font-size: 3rem;
      color: #fff;
      margin-bottom: 1rem;
    }

    .movie-year {
      font-size: 1.5rem;
      color: #fff;
      margin-bottom: 1rem;
    }

    .movie-plot {
      font-size: 1.2rem;
      color: #fff;
      text-align: center;
      width: 50%;
      margin: 0 auto;
    }
  }

  .movie-poster {
    display: block;
    max-width: 300px;
    margin-bottom: 2rem;
  }
}
.loading {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;

  &::before {
    content: "";
    display: block;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    border: 2px solid #ccc;
    border-top-color: #333;
    animation: spin 0.8s linear infinite;
  }

  @keyframes spin {
    to {
      transform: rotate(360deg);
    }
  }
}
</style>

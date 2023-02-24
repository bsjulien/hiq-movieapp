<template>
  <div class="movie-search">
    <h1 class="title">HIQ Movie Search</h1>
    <div class="search-container">
      <input
        type="text"
        v-model="searchTerm"
        placeholder="Search for a movie"
        @keydown.enter="searchMovies"
      />
      <button @click="searchMovies">Search</button>
    </div>
    <router-link to="/bookmarks">View bookmarks</router-link>
    <div class="results-container">
      <div class="movie" v-for="movie in movies" :key="movie.imdbID">
        <router-link :to="'/movie/' + movie.imdbID" class="router-link"
          ><img :src="movie.Poster" alt="Movie Poster" />
          <div class="movie-info">
            <h2 class="movie-title">{{ movie.Title }}</h2>
            <p class="movie-overview">{{ movie.Year }}</p>
          </div>
        </router-link>
        <button @click="toggleBookmark(movie)" class="bookmark-button">
          {{
            isBookmarked(movie) ? "Remove from Bookmarks" : "Add to Bookmarks"
          }}
        </button>
      </div>
    </div>
    <div v-if="loading" class="loading"></div>
  </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";

export default {
  setup() {
    const searchTerm = ref("");
    const movies = ref([]);
    const loading = ref(false);

    const bookmarks = ref(JSON.parse(localStorage.getItem("bookmarks")) || []);

    const searchMovies = () => {
      if (searchTerm.value != "") {
        loading.value = true;
        axios
          .get(
            `//www.omdbapi.com/?apikey=${process.env.VUE_APP_API_KEY}&s=${searchTerm.value}`
          )
          .then((response) => {
            movies.value = response.data.Search;
            loading.value = false;
          })
          .catch((error) => {
            console.log(error);
            loading.value = false;
          });
      }
    };

    // Bookmark movies

    const toggleBookmark = (movie) => {
      if (isBookmarked(movie)) {
        bookmarks.value = bookmarks.value.filter(
          (bm) => bm.imdbID !== movie.imdbID
        );
      } else {
        bookmarks.value.push(movie);
      }
      localStorage.setItem("bookmarks", JSON.stringify(bookmarks.value));
    };

    const isBookmarked = (movie) => {
      return bookmarks.value.some((bm) => bm.imdbID === movie.imdbID);
    };
    return {
      searchTerm,
      movies,
      searchMovies,
      loading,
      bookmarks,
      toggleBookmark,
      isBookmarked,
    };
  },
};
</script>

<style lang="scss">
.movie-search {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2rem;
  font-family: Arial, sans-serif;

  .title {
    font-size: 3rem;
    margin-bottom: 2rem;
    color: #333;
  }

  .search-container {
    display: flex;
    align-items: center;
    margin-bottom: 2rem;

    input {
      padding: 0.5rem 1rem;
      font-size: 1.5rem;
      border: 2px solid #333;
      border-radius: 4px;
      margin-right: 1rem;
      flex-grow: 1;
    }

    button {
      padding: 0.5rem 1rem;
      font-size: 1.5rem;
      background-color: #333;
      color: #fff;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
  }

  .results-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;

    .movie {
      display: flex;
      flex-direction: column;
      margin: 1rem;
      width: 300px;
      border-radius: 4px;
      overflow: hidden;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);

      a {
        text-decoration: none;
        color: #000;
      }

      img {
        width: 100%;
        height: 300px;
        object-fit: cover;
      }

      .bookmark-button {
        background-color: #f2f2f2;
        border: none;
        border-radius: 4px;
        color: #333;
        cursor: pointer;
        font-size: 16px;
        padding: 8px 16px;
        border: 2px solid #333;
        margin: 0px 10px 20px 20px;

        &:hover {
          background-color: #333;
          color: #f2f2f2;
        }
      }

      .movie-info {
        padding: 1rem;

        .movie-title {
          font-size: 1.5rem;
          margin-bottom: 1rem;
        }

        .movie-overview {
          font-size: 1rem;
          line-height: 1.5;
        }
      }
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
}
</style>

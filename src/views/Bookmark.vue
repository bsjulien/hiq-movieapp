<template>
  <div class="bookmarks">
    <h1 class="title">Bookmarks</h1>
    <div class="results-container">
      <div class="movie" v-for="movie in bookmarkedMovies" :key="movie.imdbID">
        <router-link :to="'/movie/' + movie.imdbID" class="router-link"
          ><img :src="movie.Poster" alt="Movie Poster" />
          <div class="movie-info">
            <h2 class="movie-title">{{ movie.Title }}</h2>
            <p class="movie-overview">{{ movie.Year }}</p>
          </div></router-link
        >
        <button @click="toggleBookmark(movie)" class="bookmark-button">
          Remove from Bookmarks
        </button>
      </div>
      <p v-if="bookmarks.length == 0">No bookmarks currently added</p>
    </div>
  </div>
</template>

<script>
import { ref, computed } from "vue";

export default {
  setup() {
    const bookmarks = ref(JSON.parse(localStorage.getItem("bookmarks")) || []);

    const toggleBookmark = (movie) => {
      bookmarks.value = bookmarks.value.filter(
        (bm) => bm.imdbID !== movie.imdbID
      );
      localStorage.setItem("bookmarks", JSON.stringify(bookmarks.value));
    };

    const bookmarkedMovies = computed(() => {
      return bookmarks.value;
    });

    return {
      bookmarks,
      toggleBookmark,
      bookmarkedMovies,
    };
  },
};
</script>

<style lang="scss">
.bookmarks {
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
}
</style>

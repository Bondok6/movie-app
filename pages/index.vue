<template>
  <div class="home">
    <!-- Hero -->
    <hero-section />
    <!-- Search -->
    <div class="container search">
      <input
        @keyup.enter="$fetch"
        type="search"
        placeholder="Search"
        v-model.lazy="searchInput"
      />
      <button @click="clearSearch" v-show="searchInput !== ''" class="button">
        Clear Search
      </button>
    </div>
    <!-- Loading -->
    <loading v-if="$fetchState.pending" />
    <!-- Movies -->
    <div v-else class="container movies">
      <div v-if="searchInput === ''" id="movie-grid" class="movies-grid">
        <div class="movie" v-for="(movie, index) in movies" :key="index">
          <div class="movie-img">
            <img
              :src="`https:/image.tmdb.org/t/p/w500/${movie.poster_path}`"
              alt=""
            />
            <p class="review">{{ movie.vote_average }}</p>
            <p class="overview">{{ movie.overview }}</p>
          </div>
          <div class="info">
            <p class="title">
              {{ movie.title.slice(0, 25) }}
              <span v-if="movie.title.length > 25">...</span>
            </p>
            <p class="release">
              Released:
              {{
                new Date(movie.release_date).toLocaleString('en-us', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                })
              }}
            </p>
            <nuxt-link
              :to="{ name: 'movies-movieid', params: { movieid: movie.id } }"
              class="button button-light"
              >Get More Info</nuxt-link
            >
          </div>
        </div>
      </div>
      <div v-else id="movie-grid" class="movies-grid">
        <div
          class="movie"
          v-for="(movie, index) in searchedMovies"
          :key="index"
        >
          <div class="movie-img">
            <img
              :src="`https:/image.tmdb.org/t/p/w500/${movie.poster_path}`"
              alt="movie poster"
            />
            <p class="review">{{ movie.vote_average }}</p>
            <p class="overview">{{ movie.overview }}</p>
          </div>
          <div class="info">
            <p class="title">
              {{ movie.title.slice(0, 25) }}
              <span v-if="movie.title.length > 25">...</span>
            </p>
            <p class="release">
              Released:
              {{
                new Date(movie.release_date).toLocaleString('en-us', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                })
              }}
            </p>
            <nuxt-link
              :to="{ name: 'movies-movieid', params: { movieid: movie.id } }"
              class="button button-light"
              >Get More Info</nuxt-link
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      movies: [],
      searchedMovies: [],
      searchInput: '',
    }
  },
  async fetch() {
    if (this.searchInput === '') {
      await this.getMovies()
      return
    }

    await this.searchMovies()
  },
  fetchDelay: 1000,
  methods: {
    async getMovies() {
      const data = await fetch(
        'https://api.themoviedb.org/3/movie/now_playing?api_key=a5ed8e4ca4c3bb8f1d92e46b5fb781ef&language=en-US&page=1'
      )
      const movies = await data.json()
      movies.results.forEach((movie) => {
        this.movies.push(movie)
      })
      // console.log(this.movies)
    },
    async searchMovies() {
      const data = await fetch(
        `https://api.themoviedb.org/3/search/movie?api_key=a5ed8e4ca4c3bb8f1d92e46b5fb781ef&language=en-US&page=1&query=${this.searchInput}`
      )
      const searchedMovies = await data.json()
      searchedMovies.results.forEach((movie) => {
        this.searchedMovies.push(movie)
      })
      console.log(this.searchedMovies)
    },
    clearSearch() {
      this.searchInput = ''
      this.searchedMovies = []
    },
  },
}
</script>

<style lang="scss" scoped>
.loading {
  padding-top: 120px;
  align-items: flex-start;
}

.search {
  display: flex;
  padding: 32px 16px;
  input {
    max-width: 350px;
    width: 100%;
    padding: 12px 6px;
    font-size: 14px;
    border: none;
    &:focus {
      outline: none;
    }
  }
  .button {
    border-top-left-radius: 0;
    border-bottom-left-radius: 0;
  }
}

.movies {
  padding: 32px 16px;
  .movies-grid {
    display: grid;
    column-gap: 32px;
    row-gap: 64px;
    grid-template-columns: 1fr;
    @media (min-width: 500px) {
      grid-template-columns: repeat(2, 1fr);
    }
    @media (min-width: 750px) {
      grid-template-columns: repeat(3, 1fr);
    }
    @media (min-width: 1100px) {
      grid-template-columns: repeat(4, 1fr);
    }
    .movie {
      position: relative;
      display: flex;
      flex-direction: column;
      .movie-img {
        position: relative;
        overflow: hidden;
        &:hover {
          .overview {
            transform: translateY(0);
          }
        }
        img {
          display: block;
          width: 100%;
          height: 100%;
        }
        .review {
          position: absolute;
          top: 0;
          left: 0;
          display: flex;
          justify-content: center;
          align-items: center;
          width: 40px;
          height: 40px;
          background-color: #c92502;
          color: #fff;
          border-radius: 0 0 16px 0;
          box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
            0 2px 4px -1px rgba(0, 0, 0, 0.06);
        }
        .overview {
          line-height: 1.5;
          position: absolute;
          bottom: 0;
          background-color: rgba(201, 38, 2, 0.9);
          padding: 12px;
          color: #fff;
          transform: translateY(100%);
          transition: 0.5s ease-in-out all;
        }
      }
      .info {
        margin-top: auto;
        .title {
          margin-top: 8px;
          color: #fff;
          font-size: 20px;
        }
        .release {
          margin-top: 8px;
          color: #c9c9c9;
        }
        .button {
          margin-top: 8px;
        }
      }
    }
  }
}
</style>

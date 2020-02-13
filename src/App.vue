<template>
  <div id="app">
    <div class="block-preview" style="background-image: url('images/poster-bg.jpg');">
      <div class="mainer">
        <div class="block-play"></div>
        <div class="block-caption">
          <h1>RINGS</h1>EVIL IS REBORN
        </div>
        <div
          class="block-description"
        >Paramount Pictures have just released the first trailer for the upcoming Horror movie “Rings“.</div>
      </div>
    </div>

    <div class="block-genres">
      <div class="mainer">
        <ul>
          <li
            v-for="genre in genresMenu"
            :key="genre.id"
            @click="getMovies(genre.id); genreActiveId = genre.id"
            :class="{active: genre.id === genreActiveId}"
          >{{genre.name}}</li>
        </ul>
      </div>
    </div>

    <div class="block-grid">
      <div class="mainer">
        <div class="grid-wrapper">
          <div class="grid-cell" v-for="movie in movies" :key="movie.id">
            <div class="block-card">
              <div class="label" v-if="movie.popularity > 60">Popular</div>
              <div
                class="poster"
                :style="{'background-image': 'url(https://image.tmdb.org/t/p/w500' + movie.poster_path + ')'}"
              >
                <div class="buttons">
                  <button class="btn">info</button>
                  <button class="btn">trailer</button>
                </div>
                <button class="favorite" @click="addToFavorite(movie.id)">+</button>
              </div>
              <div class="body">
                <div class="caption">{{movie.title}}</div>
                <div class="genres">{{getGenresString(movie.genre_ids)}}</div>
                <div class="raiting">
                  <div class="stars">
                    <span :style="{width: movie.vote_average *10 + '%'}"></span>
                  </div>
                  <div class="amount">{{movie.vote_average}}</div>
                </div>
              </div>
            </div>
          </div>
          <div class="grid-cell empty"></div>
          <div class="grid-cell empty"></div>
        </div>
        <button class="more" @click="getMovies(genreActiveId, ++curentPage)">
          <span class="dots"></span>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      apiUrl: "https://api.themoviedb.org/",
      apiKey: "b024c8adb8168a6a5290a61d511ff9eb",
      genres: [],
      genresMenu: [],
      movies: [],
      genreActiveId: null,
      curentPage: 1
    };
  },
  methods: {
    getGenres() {
      fetch(
        this.apiUrl +
          "3/genre/movie/list?api_key=" +
          this.apiKey +
          "&language=uk-UA"
      )
        .then(result => {
          return result.json();
        })
        .then(result => {
          this.genresMenu = result.genres.slice(0, 6);
          this.genres = result.genres;
          console.log(result);
          this.genreActiveId = result.genres[0].id;
          this.getMovies(this.genreActiveId, this.curentPage);
        });
    },
    getMovies(genreId, page) {
      fetch(
        this.apiUrl +
          "3/discover/movie?api_key=" +
          this.apiKey +
          "&language=uk-UA&sort_by=popularity.desc&include_adult=false&include_video=false&page=" +
          this.curentPage +
          "&with_genres=" +
          genreId
      )
        .then(result => {
          return result.json();
        })
        .then(result => {
          if (page > 1) {
            this.movies = this.movies.concat(result.results);
          } else {
            this.movies = result.results;
          }
        });
    },
    getGenresString(ids) {
      let result = "";
      for (let id of ids) {
        for (let genre of this.genres) {
          if (id == genre.id) {
            result += ", " + genre.name;
          }
        }
      }
      console.log(result);
      return result.slice(2, result.length);
    },
    addToFavorite(id) {
      const favoriteList = localStorage.getItem("fovorite");
      const curentList = JSON.parse(favoriteList) || [];

      if (favoriteList) {
        if (curentList.indexOf(id) < 0) {
          curentList.push(id);
        } else {
          curentList.splice(curentList.indexOf(id), 1);
        }
      } else {
        curentList.push(id);
      }
      localStorage.setItem("favourite", JSON.stringify(curentList));
    }
  },

  created: function() {
    this.getGenres();
  }
};
</script>

<style lang="scss">
@import "./src/styles/styles.scss";
</style>
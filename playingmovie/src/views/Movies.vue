<template>
  <div class="container">
    <transition name='jump'>
      <div v-if='isOpeningShow' class='opening-container'>
        <div class="logo-container">
          <img src="../../public/img/mylogo.png" alt="myLogo">
          <div class='cinema-text'>CINEMA</div>
        </div>
      </div>
    </transition>
    <transition name='slide-fade'>
      <div v-if='isTextShow' id='movie-list-container' @scroll="onScroll" class='content-container inde-scroll scrollbar'>
        <movie-card v-for='(movie, key) in moviesArray' v-bind:key='key'
                    :movieInfo="movie"></movie-card>
      </div>
    </transition>
    <router-view/>
  </div>
</template>
<style scoped>
  .logo-container {
    display: flex;
    align-items: center;
    border-radius: 10px;
    font-family:Arial, Helvetica, sans-serif;
    padding: 1rem
  }
  .cinema-text {
    color: white;
    font-weight: bold;
    font-size: 70px;
    border: 5px solid;
    border-radius: 5px;
    padding: 0.2rem
  }
  .opening-container {
    height: 100%;
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  .container {
    width: 98vw;
    height: 95%;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .content-container {
    width: 100%;
    height: 95%;
    background-color: rgba(255, 255, 255, 0);
    display: flex;
    flex-wrap: wrap;
    margin: 0.2rem
  }
  
</style>
<script>
  import MovieCard from '../components/MovieCard.vue'
  export default {
    name: 'Body',
    data () {
      return {
        isOpeningShow: false,
        isTextShow: false,
        moviesArray: [],
        currentPage: 0,
        inFetching: false,
      }
    },
    methods: {
      displayOpening () {
        setTimeout(() => {
          this.isOpeningShow = true
          setTimeout(() => {
            this.isOpeningShow = false
            this.displayMovies()
          }, 2000)
        }, 2000)
      },
      displayMovies () {
        setTimeout(() => {
          this.isTextShow = true  
        }, 500)
      },
      display() {
        /**
         * Display opening only when users first enter the page
         * To re-see the opening, clear the cookie (local storage)
         */
        if (localStorage.getItem('flag') != 'yes') {
          localStorage.setItem('flag', 'yes')
          this.displayOpening()
        } else {
          this.displayMovies()
        }
      },
      updateMovieArray () {
        /**
         * Get movie_array from API and append to current movie_aray
         */
        if (this.inFetching == true) {
          return
        } else {
          this.inFetching = true
        }
        this.currentPage += 1
        const query = `{nowPlaying(page:${this.currentPage}) {
                          movies {
                            id
                            title
                            poster_path
                            vote_average
                          }
                        }}`
        const url = "https://ion-movies.herokuapp.com/";
        const opts = {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({ query })
        };
        fetch(url, opts)
          .then(res => res.json())
          .then(res => {
            this.moviesArray = this.moviesArray.concat(res.data.nowPlaying.movies)
            this.inFetching = false
          })
          .catch(er => {
            console.log(er)
            this.inFetching = false
          });
          },
      onScroll ({ target: { scrollTop, clientHeight, scrollHeight }}) {
        if (scrollTop + clientHeight >= scrollHeight) {
          this.updateMovieArray()
        }
      }
      },
    mounted () {
      this.updateMovieArray()
      this.display()
      },
    watch: {
    },
    components: {
      'movie-card': MovieCard
    }
}
</script>

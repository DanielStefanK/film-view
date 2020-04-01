<template>
  <v-card>
    <template v-if="loading">
      <v-progress-linear indeterminate/>
    </template>
    <template v-else-if="error">
      <v-alert type="error">
        Could not load movie details
      </v-alert>
    </template>
    <template v-else>
      <div class="d-flex align-start justify-space-between">
        <v-img
          max-width="250"
          class="ma-3"
          contain 
          :src="pictureUrl(details.poster_path)"/>

        <div>
          <v-card-title
            class="headline">
            {{details.title}} ({{release}})
            <v-spacer/>
            <CreatePostDialog :data="movieData"/>
          </v-card-title>

          <v-card-subtitle v-text="details.tagline"></v-card-subtitle>
          <v-card-text v-text="details.overview"/>
          <v-card-text>
            <span class="caption">
              Genre: {{genre}}
            </span>
            <v-card light>
              <v-card-title
                class="headline">
              Studios
              </v-card-title>
              <v-card-text>
                <ul>
                  <li v-for="studio in details.production_companies" :key="studio.id" class="mr-6">
                    <StudioView  :studio="studio"/>
                  </li>
                </ul>
              </v-card-text>
            </v-card>
            <Credits v-model="credits" :movie-id="movieId"/>
          </v-card-text>
        </div>

      </div>
        <v-card-text>
        </v-card-text>
    </template>
  </v-card>
</template>

<style scoped>
ul {
  list-style-type: none;
  padding: 0;
  overflow: hidden;
}

li {
  float: left;
}
</style>

<script>
  import axios from "axios"

  import StudioView from "./StudioView"
  import Credits from "./Credits"
  import CreatePostDialog from "./CreatePostDialog"

  export default {
    components: {
      StudioView,
      Credits,
      CreatePostDialog
    },

    props: {
      movieId: {
        type: Number,
        default: 0
      }
    },

    data () {
      return {
        loading: false,
        error: false,
        details: null,
        credits: null
      }
    },

    watch: {
      movieId: {
        handler () {
          this.loadMovieDetails()
        },
        immediate: true
      }
    },

    computed: {
      genre() {
        return this.details.genres.reduce((acc, i) => {
          return acc === "" ? i.name : acc + ", " + i.name
        },"")
      },

      release () {
        return this.details.release_date.split("-")[0]
      },

      directors() {
        if (this.credits && this.credits.crew) {
          return this.credits.crew.filter((c)=>c.job === "Director")
            .reduce((acc, c) => acc == "" ? c.name : acc + ", " + c.name, "")
        }
        return null
      },

      movieData () {
        return {
          year: this.release,
          title: this.details.title,
          text: this.details.overview,
          genre: this.genre,
          directors: this.directors,
          poster: this.pictureUrl(this.details.poster_path)
        }
      }
    },

    methods: {
      loadMovieDetails () {
        if (!this.movieId) {
          return
        }
        this.loading = true
        this.error = false
        axios.get(`${process.env.VUE_APP_API_ENDPOINT}api/movie/${this.movieId}`).then((response)=> {
          if (response.data.success) {
            this.details = response.data.result
          }else {
            this.items = []
            this.error = true
          }
        }).finally (()=>{
          this.loading = false
        })
        
      },

      pictureUrl (path) {
        if (!path) {
          return
        }
        
        return `${process.env.VUE_APP_IMG_ENDPOINT}${path}`
      }
    }
  }
</script>
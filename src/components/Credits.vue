<template>
  <v-card flat>
    <v-card-title class="headline">
      Credits
    </v-card-title>
    <template v-if="loading">
        <v-progress-linear indeterminate/>
      </template>
      <template v-else-if="error">
        <v-alert type="error">
          Could not load movie details
        </v-alert>
      </template>
      <template v-else>
        <v-card>
          <v-card-text>
            <v-menu v-for="actor in cast" :key="actor.id" 
              open-on-hover>
              <template v-slot:activator="{on}">
                <v-avatar v-on="on" class="mr-2">
                  <v-img
                    v-if="actor.profile_path"
                    :src="pictureUrl(actor.profile_path)"/>
                  <template v-else>
                    {{actor.name.charAt(0)}}
                  </template>
                </v-avatar>
              </template>
              <v-card light>
                <div class="d-flex align-start justify-space-between">
                  <v-avatar v-on="on">
                    <v-img
                      v-if="actor.profile_path"
                      :src="pictureUrl(actor.profile_path)"/>
                    <template v-else>
                      {{actor.name.charAt(0)}}
                    </template>
                  </v-avatar>
                  <v-card-title>
                    <div>
                      <div class="headline">{{actor.name}}</div>
                      <div class="subtitle-1">({{actor.character}})</div>
                    </div>
                  </v-card-title>
                </div>
              </v-card>
            </v-menu>
          </v-card-text>
        </v-card>
        <v-card>
          <v-card-text>
            <v-avatar v-for="actor in crew" :key="actor.id" class="mr-2">
              <v-img
                v-if="actor.profile_path"
                :src="pictureUrl(actor.profile_path)"/>
              <template v-else>
                {{actor.name.charAt(0)}}
              </template>
            </v-avatar>
          </v-card-text>
        </v-card>
      </template>
  </v-card>
</template>

<script>
  import axios from "axios"

  export default {
    props: {
      movieId: {
        type: Number,
        default: null
      }
    },

    data() {
      return {
        crew: null,
        cast: null,
        loading: false,
        error: false
      }
    },

    watch: {
      movieId: {
        handler () {
          this.loadMovieCredits()
        },
        immediate: true
      }
    },

    methods: {
      loadMovieCredits () {
        if (!this.movieId) {
          return
        }
        this.loading = true
        this.error = false
        axios.get(`${process.env.VUE_APP_API_ENDPOINT}api/movie/credits/${this.movieId}`).then((response)=> {
          if (response.data.success) {
            this.crew = response.data.result.crew
            this.cast = response.data.result.cast
          }else {
            this.crew = null
            this.cast = null
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
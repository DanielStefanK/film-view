<template>
  <v-dialog
      v-model="dialog">
    <template v-slot:activator="{ on }">
      <v-btn v-on="on" color="primary">Create Post</v-btn>
    </template>
    <v-card>
      <v-card-text>
        <v-row>
          <v-col cols="12" md="2">
            <v-text-field
              label="Number"
              v-model="no"/>
          </v-col>
          <v-col cols="12" md="6">
            <v-text-field
              label="Title"
              v-model="title"/>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field
              label="Year"
              v-model="year"/>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field
              label="Genre"
              v-model="genre"/>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field
              label="Directors"
              v-model="directors"/>
          </v-col>
          <v-col cols="12" md="4">
            <v-text-field
              label="Amazon-Link"
              v-model="amazonlink"/>
          </v-col>
          <v-col cols="12">
            <v-text-field
              label="Trailer"
              v-model="trailer"/>
          </v-col>
          <v-col cols="12">
            <v-textarea
              label="Text"
              v-model="text"/>
          </v-col>
        </v-row>
      </v-card-text>
      <v-card-actions>
        <v-spacer/>
        <v-btn text @click="dialog = false">
          Close
        </v-btn>
        <v-btn :href="posterURL" target="_blank">
          Download Poster
        </v-btn>
        <v-btn color="primary" @click="createPost">
          Create
        </v-btn>
      </v-card-actions>
      <v-card-text>
        <v-row v-if="isLoading">
          <v-progress-linear indeterminate/>
        </v-row>
        <v-row v-if="result">
          <v-card light>
            <pre>
              {{result}}
            </pre>
            <v-card-actions>
              <v-spacer/>
              <v-btn color="success"
                @click="doCopy">
                Copy
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-row>
      </v-card-text>
    </v-card>
  </v-dialog>
</template>

<script>
  import axios from "axios"

  export default {
    props: {
      data: {
        type: Object,
        default: null
      }
    },

    watch: {
      data: {
        handler () {
          this.text = this.data.text
          this.title = this.data.title
          this.year = this.data.year
          this.genre = this.data.genre
          this.directors = this.data.directors

          this.poster = this.data.poster
        },
        immediate: true
      }
    },

    created() {
      this.no = 10
    },

    data () {
      return {
        dialog: false,
        text: '',
        no: '',
        title: '',
        year: '',
        genre: '',
        directors: '',
        amazonlink: '',
        trailer: '',
        poster: '',
        isLoading: false,
        result: null
      }
    },

    computed: {
      posterURL () {
        return `${process.env.VUE_APP_API_ENDPOINT}api/resize-img?location=${encodeURI(this.poster)}&title=${encodeURI(this.fileName)}`
      },

      fileName () {
        return `${this.title.replace(/[;:., ]/g, "-").replace(/[']/g, "")}-${this.year}`
      }
    },

    methods: {
      createPost () {
        this.isLoading = true

        axios.get(`${process.env.VUE_APP_API_ENDPOINT}api/make-template`, {
          params: {
            text: encodeURI(this.text),
            no: encodeURI(this.no),
            title: encodeURI(this.title),
            year: encodeURI(this.year),
            genre: encodeURI(this.genre),
            directors: encodeURI(this.directors),
            amazonlink: encodeURI(this.amazonlink),
            trailer: encodeURI(this.trailer),
            poster: encodeURI(this.poster)
          }
        }).then ((r)=>{
          this.result = r.data.result
        }).finally(()=> {
          this.isLoading = false
        })
      },

      doCopy: function () {
        this.$copyText(this.result)
      }
    }
  }
</script>
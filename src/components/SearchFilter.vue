<template>
  <v-autocomplete
    v-model="selectedId"
    :loading="loading"
    :items="items"
    :search-input.sync="query"
    :error="error"
    item-value="id"
    item-text="title"
    hide-no-data
    clearable
    no-filter
    hide-details
    label="Movie"
    solo-inverted
    @update:search-input="loadItems">
    <template v-slot:item="{ item }">
      <v-list-item-avatar
        color="indigo"
        class="headline font-weight-light white--text"
      >
      <v-img
        v-if="item.poster_path"
        :src="pictureUrl(item.poster_path)"/>
        <template v-else>
          {{item.title.charAt(0)}}
        </template>
      </v-list-item-avatar>
      <v-list-item-content>
        <v-list-item-title v-text="item.title"></v-list-item-title>
        <v-list-item-subtitle>{{getSubtitle (item.genre_ids)}}</v-list-item-subtitle>
      </v-list-item-content>
    </template>
  </v-autocomplete>
</template>

<script>
  import axios from "axios"
  import debounce from "lodash/debounce"

  import genreMap from '../utils/genres'

  export default {
    props: {
      value: {
        type: Number,
        default: undefined
      }
    },

    data () {
      return {
        query: "",
        loading: false,
        items: [], 
        error: false
      }
    },
    
    computed: {
      selectedId: {
        get () {
          return this.value
        }, 
        set (newVal) {
          this.$emit('input', newVal)
        }
      }
    },

    methods: {
      getSubtitle (genres) {
        if (!genres || genres.length === 0) {
          return "-"
        }

        const subtitle = genres.reduce((acc, i) => {
          return acc === "" ? genreMap[i] : acc + ", " + genreMap[i]
        },"")

        return subtitle
      },

      loadItems: debounce(function () {
        if (!this.query || this.query.trim ()=== "") {
          return
        }
        
        this.loading = true
        this.error = false

        axios.get(`${process.env.VUE_APP_API_ENDPOINT}api/search/movie`, {
          params: {
            term: encodeURI (this.query)
          }
        }).then((response)=> {
          if (response.data.success) {
            this.items = response.data.result.results
          }else {
            this.items = []
            this.error = true
          }

        }).finally (()=>{
          this.loading = false
        })
        
      }, 500),


      pictureUrl (path) {
        if (!path) {
          return
        }
        
        return `${process.env.VUE_APP_IMG_ENDPOINT}${path}`
      }
    }
  }
</script>
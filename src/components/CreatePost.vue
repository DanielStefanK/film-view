<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="12">
        <SearchFilter v-model="movieId"/>
      </v-col>
      <v-col cols="12" v-if="movieId">
        <MovieDetails :movie-id="movieId"/>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  import SearchFilter from './SearchFilter'
  import MovieDetails from './MovieDetails'

  export default {
    name: 'CreatePost',

    components: {
      SearchFilter,
      MovieDetails
    },

    data: () => ({
      movieId: null
    }),
  }
</script>

<template>
  <v-app>
    <v-app-bar
      app
      color="primary">
      <div>
        <v-img
          :src="require('./assets/cropped-logo.png')"
          max-height="50px"
          max-width="50px"
          contain
        />
      </div>
    </v-app-bar>

    <v-content>
      <CreatePost/>
    </v-content>
  </v-app>
</template>

<script>
import CreatePost from './components/CreatePost';

export default {
  name: 'App',

  components: {
    CreatePost,
  },

  data: () => ({
    //
  }),
};
</script>

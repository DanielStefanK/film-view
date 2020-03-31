<template>
  <div class="fill">
    <v-tooltip bottom v-if="studio.logo_path">
      <template v-slot:activator="{on}">
        <v-avatar tile>
          <v-img contain v-on="on" max-width="50px" :src="pictureUrl(studio.logo_path)"/>
        </v-avatar>

      </template>
      <span >
        {{studio.name}}
      </span>  
    </v-tooltip>
    <span v-else class="caption">
      {{studio.name}}
    </span>
  </div>
</template>

<style scoped>
.fill {
  height: 100%;
}
</style>

<script>
export default {
  props: {
    studio: {
      type: Object,
      default: null
    }
  },

  methods: {
    pictureUrl (path) {
        if (!path) {
          return
        }
        
        return `${process.env.VUE_APP_IMG_ENDPOINT}${path}`
      }
  }
}
</script>
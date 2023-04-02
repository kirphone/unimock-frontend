<template>
  <v-app dark>
    <app-header/>
    <v-main>
      <nuxt />
    </v-main>
  </v-app>
</template>

<script>
import AppHeader from "../components/AppHeader.vue";

export default {
  name: 'DefaultLayout',
  components: {AppHeader},
  data () {
    return {
      title: 'Vuetify.js'
    }
  }
}
</script>

<style>

</style>

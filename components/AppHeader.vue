<template>
  <v-app-bar app color="blue" dark fixed>
    <v-toolbar-title>Unimock</v-toolbar-title>
    <v-spacer></v-spacer>
    <v-toolbar-items class="d-flex justify-center flex-grow-1 nav-toolbar">
      <v-btn text to="/triggers">Триггеры</v-btn>
      <v-btn text to="/templates">Шаблоны</v-btn>
    </v-toolbar-items>
    <v-spacer></v-spacer>
  </v-app-bar>
</template>

<script>
export default {
  name: "AppHeader"
}
</script>

<style scoped>

</style>

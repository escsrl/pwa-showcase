<template>
  <section>
    <v-app-bar dense fixed app>
      <v-toolbar-title class="title">Ordini cliente</v-toolbar-title>
      <v-spacer />
      <v-btn text nuxt>
        <v-tooltip bottom>
          <template v-slot:activator="{ on }">
            <v-icon v-on="on">person</v-icon>
          </template>
          <span>Profilo</span>
        </v-tooltip>
      </v-btn>
    </v-app-bar>
  </section>
</template>

<script>
export default {
  name: 'Navbar',
  data() {
    return {}
  }
}
</script>

<style scoped>
.title {
  letter-spacing: 0.05em;
}
</style>

<template>
  <v-app>
    <v-navigation-drawer v-model="drawer" app>
      <v-list>
        <v-list-item to="/" title="Home" prepend-icon="mdi-home" />
        <v-list-item
          to="/projects"
          title="Projects"
          prepend-icon="mdi-folder"
        />
        <v-list-item
          to="/spaces"
          title="Spaces"
          prepend-icon="mdi-information"
        />
      </v-list>
    </v-navigation-drawer>

    <v-app-bar app>
      <!-- Menu button for mobile screens -->
      <v-app-bar-nav-icon @click="()=> drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>App Bar</v-toolbar-title>
    </v-app-bar>

    <v-main class="px-4">
      <v-container fluid class="px-0">
        <router-view />
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref } from "vue";

const drawer = ref(true);

//
</script>
<style scoped>

</style>
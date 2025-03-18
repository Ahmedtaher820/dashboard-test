<template>
  <v-app>
    <v-responsive class="border rounded" min-height="100vh">
      <v-app>
        <v-navigation-drawer>
          <v-list>
            <v-list-item to="/" title="Home" prepend-icon="mdi-home" />
            <v-list-item to="/projects" title="Porjects" prepend-icon="mdi-home" />
            <v-list-item
              to="/spaces"
              title="Spaces"
              prepend-icon="mdi-information"
            />
          </v-list>
        </v-navigation-drawer>

        <v-app-bar title="App bar"></v-app-bar>

        <v-main>
          <v-container>
            <router-view />
          </v-container>
        </v-main>
      </v-app>
    </v-responsive>
  </v-app>
</template>

<script setup>
//
</script>

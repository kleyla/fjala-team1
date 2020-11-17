<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <v-btn to="/" text>Vuewater</v-btn>
      <v-spacer></v-spacer>
      <v-btn to="/pedidos/registrar" text>Registro Pedido</v-btn>
    </v-app-bar>

    <v-main>
      <!-- Provides the application the proper gutter -->
      <v-container fluid>
        <!-- If using vue-router -->
        <router-view></router-view>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "App",

  data: () => ({
    //
  }),
};
</script>


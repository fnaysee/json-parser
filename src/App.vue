<template>
  <v-app>
    <v-app-bar
      app
      color="white"

    >
      <img :src="'./favicon.png'" width="40" height="40" />
      <v-app-bar-title>JSON Parser</v-app-bar-title>
      <v-spacer></v-spacer>
      <v-btn icon href="https://github.com/fnaysee/json-parser"><v-icon>mdi-github</v-icon></v-btn>
    </v-app-bar>

    <v-main>
      <parser></parser>
    </v-main>

    <v-footer absolute>
      jh
    </v-footer>
  </v-app>
</template>

<script>

import Parser from "@/components/Parser";
export default {
  name: 'App',

  components: {
    Parser
  },

  data: () => ({
    //
  }),
};
</script>

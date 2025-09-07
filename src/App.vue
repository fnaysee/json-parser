<template>
  <v-app>
    <v-app-bar
      app
      color="white"

    >
      <img :src="'/json-parser/favicon.png'" width="40" height="40" />
      <v-app-bar-title>JSON Parser</v-app-bar-title>
      <v-spacer></v-spacer>
      <v-btn icon href="https://github.com/fnaysee/json-parser"><v-icon>mdi-github</v-icon></v-btn>
    </v-app-bar>

    <v-main>
      <parser></parser>
    </v-main>

    <v-footer
        absolute

        class="justify-center"
        style="text-align: center; color: #888888; font-size: 68.8%">
      Made by <v-icon  class="px-1 blinking-heart" size="12">mdi-heart</v-icon>  Farhan
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

<style lang="scss">
.blinking-heart {
  &:hover {
    &:before{
      color: red !important;
      transform: scale(2);
      animation-name: bounce;

      -webkit-animation-duration: .7s;
      animation-duration: .7s;
      -webkit-animation-fill-mode: both;
      animation-fill-mode: both;
      -webkit-animation-timing-function: linear;
      animation-timing-function: linear;
      animation-iteration-count: infinite;
      -webkit-animation-iteration-count: infinite;
    }
  }
}

@keyframes bounce {
  0% {
    transform: scale(1);
  }
  30% {
    transform: scale(1.2);
  }
  70% {
    transform: scale(.9);
  }
  100% {
    transform: scale(1);
  }
}
</style>

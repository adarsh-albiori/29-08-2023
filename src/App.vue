<template>
  <v-app>
    <v-main>
      <BingoBoardCard />
    </v-main>
  </v-app>
</template>

<script>
import BingoBoardCard from "./components/BingoBoardCard.vue";

export default {
  name: "App",

  components: {
    BingoBoardCard,
  },

  data: () => ({
    //
  }),
};
</script>

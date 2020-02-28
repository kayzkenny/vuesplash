<template>
  <v-app>
    <v-content>
      <Splash />
    </v-content>
  </v-app>
</template>

<script>
import Splash from "./components/Splash";

export default {
  name: "App",
  components: {
    Splash
  }
};
</script>

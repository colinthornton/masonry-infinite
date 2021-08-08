<template>
  <main>
    <img v-for="img in images" :key="img.key" :src="img.src" />
  </main>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

const randInt = ({ min = 0, max = 1 }): number =>
  Math.floor(Math.random() * (max - min + 1) + min);

@Component
export default class App extends Vue {
  images = Array.from({ length: 10 }, (_, i) => ({
    key: i,
    src: `https://source.unsplash.com/random/${randInt({
      min: 100,
      max: 500,
    })}`,
  }));
}
</script>

<style lang="scss">
html,
body {
  width: 100%;
  height: 100%;
  display: grid;
  place-items: center;
}

main {
  width: 320px;
  height: 568px;
  border: 1px solid black;

  padding: 15px;
}

img {
  max-width: 40%;
}
</style>

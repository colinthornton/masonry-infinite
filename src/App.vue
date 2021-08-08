<template>
  <main>
    <section class="list" :class="{ loaded: allImgsLoaded }">
      <img
        v-for="img in images"
        :key="img.key"
        class="list-item"
        :src="img.src"
        @load="handleImgLoad"
      />
    </section>
  </main>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Masonry from "masonry-layout";

const randInt = ({ min = 0, max = 1 }): number =>
  Math.floor(Math.random() * (max - min + 1) + min);

@Component
export default class App extends Vue {
  images = Array.from({ length: 10 }, (_, i) => ({
    key: i,
    src: `https://source.unsplash.com/random/${randInt({
      min: 100,
      max: 500,
    })}x${randInt({
      min: 100,
      max: 500,
    })}`,
  }));
  masonry?: Masonry;
  loadEvents: Event[] = [];

  get allImgsLoaded(): boolean {
    return this.loadEvents.length === this.images.length;
  }

  mounted(): void {
    this.masonry = new Masonry(".list", {
      itemSelector: ".list-item",
      columnWidth: 140,
      gutter: 15,
      initLayout: false,
    });
  }

  handleImgLoad(e: Event): void {
    this.loadEvents.push(e);
    if (this.allImgsLoaded) {
      this.masonry?.layout?.();
    }
  }
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

  padding-top: 15px;
  padding-left: 15px;
  overflow-y: scroll;
}

.list {
  width: 100%;
  min-height: 100%;

  opacity: 0;
  transition: opacity 200ms;
  &.loaded {
    opacity: 1;
  }
}

.list-item {
  width: 140px;
  height: auto;
  margin-bottom: 15px;
}
</style>

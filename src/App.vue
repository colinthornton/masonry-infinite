<template>
  <main
    v-infinite-scroll="appendImages"
    infinite-scroll-disabled="isLoadingImages"
    infinite-scroll-distance="200"
    :style="`--column-width: ${columnWidth}px; --gutter: ${gutter}px`"
  >
    <section class="list">
      <img
        v-for="(img, i) in appendedImages"
        :key="img.key"
        class="list-item"
        :class="{ show: i < layoutCompleteCount }"
        :style="`--transition-delay: ${(i % pageSize) * 80}ms`"
        :src="img.src"
        @load="handleImgLoad"
      />
      <p v-if="isLoadingImgs" class="loading">Loading...</p>
    </section>
  </main>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Masonry from "masonry-layout";
// @ts-expect-error no types
import infiniteScroll from "vue-infinite-scroll";

const randInt = ({ min = 0, max = 1 }): number =>
  Math.floor(Math.random() * (max - min + 1) + min);

@Component({
  directives: {
    infiniteScroll,
  },
})
export default class App extends Vue {
  masonry?: Masonry;
  readonly columnWidth = 140;
  readonly gutter = 15;

  images = Array.from({ length: 100 }, (_, i) => ({
    key: i,
    src: `https://source.unsplash.com/random/${randInt({
      min: 100,
      max: 500,
    })}`,
  }));
  readonly pageSize = 10;
  appendedCount = this.pageSize;
  loadEventCount = 0;
  layoutCompleteCount = 0;

  get appendedImages(): { key: number; src: string }[] {
    return this.images.slice(0, this.appendedCount);
  }

  get isLoadingImgs(): boolean {
    return this.loadEventCount < this.appendedCount;
  }

  mounted(): void {
    this.masonry = new Masonry(".list", {
      itemSelector: ".list-item",
      columnWidth: this.columnWidth,
      gutter: this.gutter,
      initLayout: false,
      transitionDuration: 0,
    });
  }

  handleImgLoad(): void {
    this.loadEventCount++;
    if (this.loadEventCount === this.appendedCount) {
      this.masonry?.reloadItems?.();
      this.masonry?.layout?.();
      this.layoutCompleteCount = this.appendedCount;
    }
  }

  appendImages(): void {
    this.appendedCount = Math.min(
      this.layoutCompleteCount + this.pageSize,
      this.images.length
    );
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
  position: relative;
  width: 320px;
  height: 568px;
  border: 1px solid black;

  padding-top: var(--gutter);
  padding-left: var(--gutter);
  overflow-y: scroll;
}

.list {
  width: 100%;
  min-height: 100%;
}

.list-item {
  width: var(--column-width);
  height: auto;
  margin-bottom: var(--gutter);

  opacity: 0;
  transition: opacity 200ms var(--transition-delay);
  &.show {
    opacity: 1;
  }
}

.loading {
  position: absolute;
  width: 100%;
  background: pink;
  color: blue;
  left: 0;
  bottom: 0;
  z-index: 10;
}
</style>

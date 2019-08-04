<template>
  <div
    class="chapter"
    v-html="book"
    :style="{'transform':`translateX(${translateX}px)`,'column-gap':`${columnGap}px`,'transition':`transform ${transitionDuration}s`}"
    ref="chapterZone"
  ></div>
</template>

<script>
export default {
  name: "BookChapter",
  props: {
    content: String,
    title: String
  },
  data() {
    return {page: 0};
  },
  methods: {
    prevPage() {},
    nextPage() {}
  }
};
</script>

<style scoped>
</style>

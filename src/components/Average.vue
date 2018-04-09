<template>
  <div class="avg">
    <h2>Average {{title}}</h2>
    <h2 class="mpg">{{avg}}</h2>
  </div>
</template>


<script>
export default {
  name: "Average",
  props: ["title", "avg"],
  data() {
    return {};
  }
};
</script>

<style scoped>
.avg {
  margin: 0 auto;
  padding: 1rem;
}
.mpg {
  color: #66bb6a;
}
</style>

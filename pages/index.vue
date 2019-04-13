<template>
  <section class="container">
    <div class="jumbotron">
      <h1 class="display-3">ua-factory</h1>
      <p class="lead">
        This is a simple hero unit, a simple jumbotron-style component for
        calling extra attention to featured content or information.
      </p>
    </div>
    <div>
      <input type="text" v-model="ua" class="ua-text">
      <button v-on:click="copy">クリップボードにコピー</button>
    </div>
  </section>
</template>

<script>
import UserAgent from "user-agents";

export default {
  asyncData(context) {
    const userAgent = new UserAgent();
    return { ua: userAgent.toString() };
  },
  methods: {
    copy: function() {
      console.log(1);
    }
  }
};
</script>

<style>
.ua-text {
  width: 100%;
}
</style>

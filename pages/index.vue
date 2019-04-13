<template>
  <section class="container mt-4">
    <div class="jumbotron">
      <h1 class="display-3">ua-factory</h1>
      <p class="lead">ユーザーエージェントをランダムに生成します。</p>
    </div>
    <input type="text" v-model="ua" id="ua-text" class="form-control mb-2">
    <button class="btn btn-primary" @click="reload">再生成</button>
    <button class="btn-copy btn btn-success" data-clipboard-target="#ua-text">クリップボードにコピー</button>
  </section>
</template>

<script>
import UserAgent from "user-agents";
import clipboard from "clipboard";

export default {
  asyncData(context) {
    const userAgent = new UserAgent();
    return {
      ua: userAgent.toString()
    };
  },
  methods: {
    reload: function() {
      const ua = new UserAgent().toString();
      this.$data.ua = ua;
    }
  },
  mounted: function() {
    this.clipBoard = new clipboard(".btn-copy");
    this.clipBoard.on("success", function(e) {
      console.log("copied!");
      e.clearSelection();
    });
    this.clipBoard.on("error", function(e) {});
  }
};
</script>

<style>
</style>

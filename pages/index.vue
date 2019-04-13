<template>
  <section class="container mt-4">
    <div class="jumbotron">
      <h1 class="display-3">ua-factory</h1>
      <p class="lead">ユーザーエージェントをランダムに生成します。</p>
    </div>
    <form>
      <div class="form-group">
        <p>デバイス</p>
        <input type="checkbox" id="pc" value="desktop" v-model="devices" v-on:change="reload">
        <label for="pc">PC</label>
        <input type="checkbox" id="mobile" value="mobile" v-model="devices" v-on:change="reload">
        <label for="mobile">スマートフォン</label>
        <input type="checkbox" id="tablet" value="tablet" v-model="devices" v-on:change="reload">
        <label for="tablet">タブレット</label>
      </div>
      <div class="form-group">
        <input type="text" v-model="ua_single" id="ua-text" class="form-control mb-2">
        <button class="btn btn-primary" @click="reload">再生成</button>
        <button class="btn-copy btn btn-success" data-clipboard-target="#ua-text">クリップボードにコピー</button>
      </div>
    </form>
  </section>
</template>

<script>
import UserAgent from "user-agents";
import clipboard from "clipboard";

export default {
  data() {
    return {
      devices: ["desktop", "mobile", "tablet"]
    };
  },
  async asyncData(context) {
    const ua = new UserAgent();
    return {
      ua: ua,
      ua_single: ua.random().userAgent
    };
  },
  methods: {
    reload: function() {
      this.$data.ua = new UserAgent(data => {
        return this.$data.devices.indexOf(data.deviceCategory) >= 0;
      });
      this.$data.ua_single = this.$data.ua.random().userAgent;
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

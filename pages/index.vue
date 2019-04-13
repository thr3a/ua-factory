<template>
  <section class="container mt-4">
    <div class="jumbotron">
      <h1 class="display-3">ua-factory</h1>
      <p class="lead">ユーザーエージェントをランダムに生成します。</p>
    </div>
    <div class="form-group">
      <p>デバイス</p>
      <input type="radio" id="all" value="all" v-model="device" v-on:change="reload">
      <label for="all">全て</label>
      <input type="radio" id="pc" value="desktop" v-model="device" v-on:change="reload">
      <label for="pc">PC</label>
      <input type="radio" id="mobile" value="mobile" v-model="device" v-on:change="reload">
      <label for="mobile">スマートフォン</label>
      <input type="radio" id="tablet" value="tablet" v-model="device" v-on:change="reload">
      <label for="tablet">タブレット</label>
    </div>
    <div class="form-group">
      <input type="text" :value="ua_single" id="ua-text" class="form-control mb-2">
      <button class="btn btn-primary" @click="reload">再生成</button>
      <button class="btn-copy btn btn-success" data-clipboard-target="#ua-text">クリップボードにコピー</button>
    </div>
    <div class="form-group">
      <textarea id="ua-texts" rows="10" :value="ua_multiple"></textarea>
    </div>
  </section>
</template>

<script>
import UserAgent from "user-agents";
import clipboard from "clipboard";

export default {
  data() {
    return {
      device: "all"
    };
  },
  async asyncData(context) {
    const ua = new UserAgent();
    return {
      ua: ua,
      ua_single: ua.random().userAgent,
      ua_multiple: Array.from(Array(10), (v, k) => ua.random().userAgent).join("\n")
    };
  },
  methods: {
    reload: function() {
      const filter = {};
      if (this.$data.device !== "all") {
        filter.deviceCategory = this.$data.device;
      }
      // const filter = [
      //   /Safari/,
      //   {
      //     platform: "MacIntel"
      //   }
      // ];
      this.$data.ua = new UserAgent(filter);
      // this.$data.ua = new UserAgent(data => {
      //   return this.$data.device.indexOf(data.deviceCategory) >= 0;
      // });
      this.$data.ua_single = this.$data.ua.random().userAgent;
      this.$data.ua_multiple = Array.from(Array(10), (v, k) => this.$data.ua.random().userAgent).join("\n")
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
#ua-texts {
  width: 100%;
}
</style>

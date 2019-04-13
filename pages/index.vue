<template>
  <section class="container mt-4">
      <h1 class="display-3">ua-factory</h1>
      <p class="lead">ユーザーエージェントをランダムに生成します。</p>
      <hr />
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
      <p>表示オプション</p>
      <input type="radio" id="list" value="list" v-model="multi_view">
      <label for="list">一覧表示</label>
      <input type="radio" id="array_double" value="array_double" v-model="multi_view">
      <label for="array_double">ダブルクォーテーションで囲った配列</label>
      <input type="radio" id="array_single" value="array_single" v-model="multi_view">
      <label for="array_single">シングルクォーテーションで囲った配列</label>
    </div>

    <div class="form-group">
      <textarea id="ua-texts" rows="10" :value="ua_multiple | toList(multi_view)"></textarea>
    </div>
  </section>
</template>

<script>
import UserAgent from "user-agents";
import clipboard from "clipboard";

export default {
  data() {
    return {
      device: "all",
      multi_view: "list"
    };
  },
  async asyncData(context) {
    const ua = new UserAgent();
    return {
      ua: ua,
      ua_single: ua.random().userAgent,
      ua_multiple: Array.from(Array(10), (v, k) => ua.random().userAgent)
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
      this.$data.ua_multiple = Array.from(
        Array(10),
        (v, k) => this.$data.ua.random().userAgent
      );
    }
  },
  mounted: function() {
    this.clipBoard = new clipboard(".btn-copy");
    this.clipBoard.on("success", function(e) {
      console.log("copied!");
      e.clearSelection();
    });
    this.clipBoard.on("error", function(e) {});
  },
  filters: {
    toList: function(array, type) {
      if (type === "list") {
        return array.join("\n");
      } else if (type === "array_double") {
        return JSON.stringify(array, null, "  ");
      } else if (type === "array_single") {
        return JSON.stringify(array, null, "  ").replace(/"/g, "'");
      }
    }
  }
};
</script>

<style>
#ua-texts {
  width: 100%;
}
</style>

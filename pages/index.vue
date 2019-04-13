<template>
  <section class="container mt-4 shadow-lg p-3 mb-5 bg-white rounded">
    <h1 class="display-3">ua-factory</h1>
    <p class="lead">ユーザーエージェントをランダムに生成します。テスト等にどうぞ。</p>
    <div class="form-group mb-4">
      <input type="text" :value="ua_single" id="ua-text" class="form-control">
      <button class="btn-copy btn btn-info" data-clipboard-target="#ua-text">クリップボードにコピー</button>
    </div>

    10件一括版
    <div class="form-group">
      <textarea id="ua-texts" rows="5" :value="ua_multiple | formatMulti(multi_view)"></textarea>
      <button class="btn-copy btn btn-info" data-clipboard-target="#ua-texts">クリップボードにコピー</button>
    </div>

    <div class="form-group">
      <button class="btn btn-lg btn-primary mx-auto d-block" @click="reload">もう一回生成する！</button>
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
      <p>一括版表示オプション</p>
      <input type="radio" id="list" value="list" v-model="multi_view">
      <label for="list">そのまま</label>
      <input type="radio" id="array_double" value="array_double" v-model="multi_view">
      <label for="array_double">ダブルクォーテーションで囲った配列</label>
      <input type="radio" id="array_single" value="array_single" v-model="multi_view">
      <label for="array_single">シングルクォーテーションで囲った配列</label>
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
    formatMulti: function(array, type) {
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
label {
  margin-right: 10px;
}
</style>

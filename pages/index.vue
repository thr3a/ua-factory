<template>
  <section class="container mt-4 shadow-lg p-3 mb-5 bg-white rounded">
    <h1 class="display-3">
      ua-factory
    </h1>
    <p class="lead">
      ユーザーエージェントをランダムに生成します。テスト等にどうぞ。
    </p>
    <div class="form-group mb-4">
      <input id="ua-text" type="text" :value="ua_single" class="form-control">
      <button class="btn-copy btn btn-info" data-clipboard-target="#ua-text">
        クリップボードにコピー
      </button>
    </div>１０個一括版
    <div class="form-group">
      <textarea id="ua-texts" rows="5" :value="ua_multiple | formatMulti(multi_view)" />
      <button class="btn-copy btn btn-info" data-clipboard-target="#ua-texts">
        クリップボードにコピー
      </button>
    </div>

    <div class="form-group">
      <button class="btn btn-lg btn-primary mx-auto d-block" @click="reload">
        もう一回生成する！
      </button>
    </div>

    <div class="form-group">
      <p>デバイス</p>
      <input id="all" v-model="device" type="radio" value="all" @change="reload">
      <label for="all">全て</label>
      <input id="pc" v-model="device" type="radio" value="desktop" @change="reload">
      <label for="pc">PC</label>
      <input id="mobile" v-model="device" type="radio" value="mobile" @change="reload">
      <label for="mobile">スマートフォン</label>
      <input id="tablet" v-model="device" type="radio" value="tablet" @change="reload">
      <label for="tablet">タブレット</label>
    </div>
    <div class="form-group">
      <p>OS</p>
      <input id="all" v-model="platform" type="radio" value="all" @change="reload">
      <label for="all">全て</label>
      <input id="windows" v-model="platform" type="radio" value="windows" @change="reload">
      <label for="windows">Windows</label>
      <input id="mac" v-model="platform" type="radio" value="mac" @change="reload">
      <label for="mac">Mac OS</label>
    </div>
    <div class="form-group">
      <p>一括版表示オプション</p>
      <input id="list" v-model="multi_view" type="radio" value="list">
      <label for="list">そのまま</label>
      <input id="array_double" v-model="multi_view" type="radio" value="array_double">
      <label for="array_double">ダブルクォーテーションで囲った配列</label>
      <input id="array_single" v-model="multi_view" type="radio" value="array_single">
      <label for="array_single">シングルクォーテーションで囲った配列</label>
    </div>
  </section>
</template>

<script>
import UserAgent from 'user-agents';
import clipboard from 'clipboard';

export default {
  filters: {
    formatMulti: function(array, type) {
      if (type === 'list') {
        return array.join('\n');
      } else if (type === 'array_double') {
        return JSON.stringify(array, null, '  ');
      } else if (type === 'array_single') {
        return JSON.stringify(array, null, '  ').replace(/"/g, '\'');
      }
    }
  },
  data() {
    return {
      ua: '',
      device: 'all',
      platform: 'all',
      multi_view: 'list',
      ua_single: '',
      ua_multiple: []
    };
  },
  mounted: function() {
    this.clipBoard = new clipboard('.btn-copy');
    this.clipBoard.on('success', function(e) {
      console.log('copied!');
      e.clearSelection();
    });
    this.clipBoard.on('error', function(e) {});
    this.reload();
  },
  methods: {
    reload: function() {
      const filter = {};
      if (this.$data.device !== 'all') {
        filter.deviceCategory = this.$data.device;
      }
      let platform;
      switch (this.$data.platform) {
      case 'windows':
        platform = 'Windows';
        break;
      case 'mac':
        platform = 'Macintosh';
        break;
      default:
        platform = '.+';
      }
      this.$data.ua = new UserAgent(new RegExp(platform), filter);
      this.$data.ua_single = this.$data.ua.random().userAgent;
      this.$data.ua_multiple = Array.from(
        Array(10),
        (v, k) => this.$data.ua.random().userAgent
      );
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

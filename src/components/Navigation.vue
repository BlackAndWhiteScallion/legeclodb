<template>
  <b-navbar type="dark" variant="dark" id="navigation">
    <b-navbar-brand to="/">れじぇくろDB</b-navbar-brand>
    <b-navbar-nav>
      <b-nav-item to="/main.html">メインキャラ</b-nav-item>
      <b-nav-item to="/support.html">サポートキャラ</b-nav-item>
      <b-nav-item to="/item.html">アイテム</b-nav-item>
      <b-nav-item to="/lookup.html">組み合わせ検索</b-nav-item>
      <b-nav-item to="/battle.html">バトルシミュレータ</b-nav-item>
      <b-nav-item to="/about.html">解説</b-nav-item>
    </b-navbar-nav>
    <b-navbar-nav class="ml-auto">
      <b-nav-item class="icon" href="https://github.com/legeclodb/legeclodb" target="_blank">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32" width="24" height="24" focusable="false" role="img" class="navbar-nav-svg">
          <title>GitHub</title>
          <g fill="currentColor">
            <path fill-rule="evenodd" clip-rule="evenodd" d="M16,0.4c-8.8,0-16,7.2-16,16c0,7.1,4.6,13.1,10.9,15.2 c0.8,0.1,1.1-0.3,1.1-0.8c0-0.4,0-1.4,0-2.7c-4.5,1-5.4-2.1-5.4-2.1c-0.7-1.8-1.8-2.3-1.8-2.3c-1.5-1,0.1-1,0.1-1 c1.6,0.1,2.5,1.6,2.5,1.6c1.4,2.4,3.7,1.7,4.7,1.3c0.1-1,0.6-1.7,1-2.1c-3.6-0.4-7.3-1.8-7.3-7.9c0-1.7,0.6-3.2,1.6-4.3 c-0.2-0.4-0.7-2,0.2-4.2c0,0,1.3-0.4,4.4,1.6c1.3-0.4,2.6-0.5,4-0.5c1.4,0,2.7,0.2,4,0.5C23.1,6.6,24.4,7,24.4,7 c0.9,2.2,0.3,3.8,0.2,4.2c1,1.1,1.6,2.5,1.6,4.3c0,6.1-3.7,7.5-7.3,7.9c0.6,0.5,1.1,1.5,1.1,3c0,2.1,0,3.9,0,4.4 c0,0.4,0.3,0.9,1.1,0.8C27.4,29.5,32,23.5,32,16.4C32,7.6,24.8,0.4,16,0.4z"></path>
          </g>
        </svg>
      </b-nav-item>
    </b-navbar-nav>
  </b-navbar>
</template>

<script>
import * as lut from "./utils.js";

export default {
  name: 'Navigation',
  props: {
  },

  data() {
    return {
      devmode: false,
    };
  },

  mounted() {
    this.devmode = process.env.NODE_ENV === 'development';
    this.checkNewMessage();
  },

  methods: {
    checkNewMessage() {
      const toast = () => {
        this.$bvToast.toast("購読中のスレッドに新しいメッセージが投稿されています。", {
          toaster: 'b-toaster-bottom-left',
          noAutoHide: true,
          appendToast: true,
          variant: 'danger',
        });
      };

      let threads = lut.getSubscribedThreads();
      if (threads.length) {
        let last = lut.getSubscribeLastCheckTime();
        if (!last) {
          lut.updateSubscribeLastCheck();
        }
        else {
          const minimumInterval = 10 * 60 * 1000; // 10 分は間を置く
          if (new Date().getTime() - last.getTime() > minimumInterval) {
            let date = lut.toSQLDateTime(last);
            fetch(`${lut.MessageServer}?mode=chkm&date=${encodeURIComponent(date)}`)
              .then(a => a.json())
              .then(list => {
                for (const r of list) {
                  if (threads.includes(r.thread)) {
                    toast();
                    break;
                  }
                }
              });
            lut.updateSubscribeLastCheck();
          }
        }
      }
    },
  },
};
</script>

<style scoped>
.navbar {
  padding: 0.25rem 1rem;
}

.navbar-brand {
  font-size: 1.75em;
  margin-left: 1.0rem;
  padding-top: 2px;
  padding-bottom: 2px;
}

.nav-link {
  font-size: 1.25em;
  color: rgba(255, 255, 255, 0.8) !important;
  white-space: nowrap;
}
.nav-link:hover, .nav-link:focus {
  color: rgba(255, 255, 255, 0.5) !important;
}
.icond {
  margin: 0 0;
}


</style>

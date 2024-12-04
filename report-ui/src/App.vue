<template>
  <div id="app">
    <router-view v-if="isRouterAlive" />
  </div>
</template>

<script>
import "@/assets/iconfont/iconfont.css";
import "@/assets/iconfont2/iconfont.css";
import { initDictToLocalstorage } from "@/api/dict-data";
export default {
  name: "App",
  provide() {
    return {
      reload: this.reload
    };
  },
  data() {
    return {
      isRouterAlive: false
    };
  },
  watch: {
    $route(to, form) {
      if (to.path == "/login") {
        this.queryDictName();
      }
    }
  },
  computed: {},
  created() {
    this.queryDictName();
  },
  methods: {
    queryDictName() {
      // 初始化数据字典到浏览器本地缓存
      initDictToLocalstorage(() => {
        this.isRouterAlive = true;
      });
    },
    reload() {
      this.isRouterAlive = false;
      this.$nextTick(function () {
        this.isRouterAlive = true;
      });
    }
  }
};
</script>
<style lang="css">

.el-scrollbar__view .el-select-dropdown__item.hover,
.el-select-dropdown__item:hover {
  background: rgba(236, 242, 255, 1);
}

.el-range-editor.is-active,
.el-range-editor.is-active:hover,
.el-select .el-input.is-focus .el-input__inner {
  border-color: rgba(29, 64, 175, 1);
}

/* .el-scrollbar__bar.is-vertical {
  width: 6px;
  height: 262px;
  top: 2px;
  background: rgba(211, 217, 222, 1);
} */

.el-scrollbar__thumb {
  background: rgb(29, 64, 175);

}
</style>

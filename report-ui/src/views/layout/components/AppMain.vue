<template>
  <section class="app-main">
    <transition name="fade-transform" mode="out-in">
      <!-- or name="fade" -->
      <!-- <router-view :key="key"></router-view> -->
      <keep-alive :include="cacheViews">
        <router-view></router-view>
      </keep-alive>
    </transition>
  </section>
</template>

<script>
import {mapGetters} from "vuex"
export default {
  name: 'AppMain',
  computed: {
    ...mapGetters(['cacheViews']),
    key() {
      return this.$route.name !== undefined ? this.$route.name + +new Date() : this.$route + +new Date()
    }
  }
}
</script>

<style scoped>
.app-main {
  position: fixed;
  left: 263px;
  top: 60px;
  width: calc(100% - 263px);
  height: calc(100vh - 60px);
}
</style>

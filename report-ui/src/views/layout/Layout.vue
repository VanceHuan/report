<template>
  <div :class="classObj" class="app-wrapper">
    <sidebar/>
    <div >
      <navbar/>
      <app-main/>
    </div>
  </div>
</template>

<script>
import { Navbar, Sidebar, AppMain } from './components'
import ResizeMixin from './mixin/ResizeHandler'
import {mapActions} from "vuex"
import {delStorageItem} from '@/utils/storage.js'

export default {
  name: 'Layout',
  components: {
    Navbar,
    Sidebar,
    AppMain
  },
  watch: {
    $route:{
      handler(val, oldVal){
        if(val.name != 'realTimeLog' && val.name != 'deviceInfo') {
          delStorageItem('deviceInfo')
        }
        if(val.name != 'helpCenter' && val.name != "helpCenterEdit") {
          delStorageItem('helpCenterDetail')
        }
        this.addCachedView(val)
      },
      immediate: true
    }
  },
  mixins: [ResizeMixin],
  computed: {
    sidebar() {
      return this.$store.state.app.sidebar
    },
    device() {
      return this.$store.state.app.device
    },
    classObj() {
      return {
        hideSidebar: !this.sidebar.opened,
        openSidebar: this.sidebar.opened,
        withoutAnimation: this.sidebar.withoutAnimation,
        mobile: this.device === 'mobile'
      }
    }
  },
  methods: {
    ...mapActions(['addCachedView']),
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
  @import "src/assets/styles/mixin.scss";
  .app-wrapper {
    @include clearfix;
    position: relative;
    height: 100%;
    width: 100%;
    background: url("https://img.js.design/assets/img/674433d15a16d2d8d64014f1.jpg#207706b2e51b30fb61991c66e6ecede3");
    background-size: cover;
  }
</style>

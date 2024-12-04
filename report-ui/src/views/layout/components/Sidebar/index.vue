<template>
  <div class="wrap">
    <div class="admin-title">
      <img src="../../../../assets/images/logo.png" />
      <span class="version">跃境智幕平台</span>
    </div>
    <div class="menu-wrap">
      <div v-for="menu in menus" :key="menu.name" :class="{ 'menu-item': true, active: menu.active }"
        @click="menuClick(menu)">
        <div class=" item">
          <img :src="menu.active ? menu.actIcon : menu.icon" alt="">
          <span>{{ menu.name }}</span>
        </div>
      </div>

    </div>
    <!-- <div class="collapse-wrap">
      <img src="../../../../../static/close_icon.png" alt="">
      <span>收起导航</span>
    </div> -->
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import path from 'path'
import { isExternal } from '@/utils'
import AppLink from './Link'

export default {
  components: { AppLink },
  data() {
    return {
      onlyOneChild: null,
      menus: [
        {
          name: '首页',
          icon: '../../../../../static/home_icon_dark.png',
          actIcon: '../../../../../static/home_icon.png',
          active: true,
          path: '/index'
        },
        {
          name: '报表设计',
          icon: '../../../../../static/table_icon_dark.png',
          actIcon: '../../../../../static/table_icon.png',
          active: false,
          path: '/report/datasource'
        },
        {
          name: '系统设置',
          icon: '../../../../../static/sys_icon_dark.png',
          actIcon: '../../../../../static/sys_icon.png',
          active: false,
          path: '/system/file'
        }
      ]
    }
  },
  watch: {
    $route(to, from) {
      this.menus.forEach(item => {
        item.active = false
      })
      const currentMainPath = '/' + to.path.split('/')[1]
      const activeMenu = this.menus.find(item => {
        const menuMainPath = '/' + item.path.split('/')[1]
        return menuMainPath === currentMainPath
      })
      if (activeMenu) {
        activeMenu.active = true
      }
    }
  },
  computed: {
    ...mapGetters(["sidebar"]),
    routes() {
      return this.$router.options.routes;
    },
    isCollapse() {
      return !this.sidebar.opened;
    }
  },
  methods: {
    goBigScreen() {
      let routeUrl = this.$router.resolve({
        path: "/report/bigScreen"
      });
      window.open(routeUrl.href, "_blank");
    },
    hasOneShowingChild(children, parent) {
      const showingChildren = children.filter((item) => {
        if (item.hidden) {
          return false
        } else {
          // Temp set(will be used if only has one showing child)
          this.onlyOneChild = item
          return true
        }
      })
      // When there is only one child router, the child router is displayed by default
      if (showingChildren.length === 1) {
        return true
      }

      // Show parent if there are no child router to display
      if (showingChildren.length === 0) {
        this.onlyOneChild = { ...parent, path: '', noShowingChildren: true }
        return true
      }

      return false
    },
    resolvePath(basePath, routePath) {
      if (this.isExternalLink(routePath)) {
        return routePath
      }
      return path.resolve(basePath, routePath)
    },
    isExternalLink(routePath) {
      return isExternal(routePath)
    },
    menuClick(menu) {
      this.menus.forEach(item => {
        item.active = false
      })
      menu.active = true;
      this.$router.push(menu.path);
    }
  }
};
</script>

<style lang="scss" scoped>
.wrap {
  position: fixed;
  left: 0;
  top: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 100vh;
  width: 263px;
  padding-left: 16px;
  padding-right: 16px;
  padding-top: 27px;

  .admin-title {
    display: flex;
    align-items: center;
    padding-bottom: 24px;

    img {
      width: 21px;
      height: 21px;
    }

    .version {
      font-size: 20px;
      font-weight: 500;
      color: #2F3135;
      margin-left: 10px;
    }
  }

  .admin-title:hover {
    cursor: pointer;
  }

  .menu-wrap {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;

    .menu-item {
      width: 100%;
      height: 40px;
      margin-bottom: 20px;
      display: flex;
      align-items: center;
      padding-left: 16px;
      border-radius: 8px;

      .item {
        display: flex;
        flex-direction: row;
        align-items: center;


        img {
          width: 16px;
          height: 13px;
          margin-right: 12px;
        }

        span {
          font-size: 14px;
          font-weight: 500;
          color: #35404F;
        }

      }

      &.active {
        background: #fff;
      }
    }


  }

  .collapse-wrap {
    display: flex;
    align-items: center;
    width: 100%;
    position: absolute;
    left: 31px;
    bottom: 48px;
    font-size: 14px;
    color: #35404F;

    img {
      width: 20px;
      height: 20px;
      margin-right: 12px;
    }
  }
}
</style>

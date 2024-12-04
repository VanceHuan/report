<template>
  <div class="menu">
    <div :class="menu.active ? 'menu-item active' : 'menu-item'" v-for="menu in menus" :key="menu.name" @click="menuClick(menu)">
      <div v-show="menu.active" class="bar"></div>
      <img :src="menu.active ? menu.actIcon : menu.icon" alt="">
      <span>{{ menu.name }}</span>
    </div>
  </div>
</template>
<script>
export default {

  data() {
    return {
      menus: [
        {
          name: '数据源',
          active: true,
          path: '/report/datasource',
          icon: require('../../static/data_ori_icon_dark.png'),
          actIcon: require('../../static/data_ori_icon.png'),

        },
        {
          name: '数据集',
          active: false,
          path: '/report/resultset',
          icon: require('../../static/data_col_icon_dark.png'),
          actIcon: require('../../static/data_col_icon.png'),
        },
        {
          name: '报表管理',
          active: false,
          path: '/report/report',
          icon: require('../../static/report_man_icon_dark.png'),
          actIcon: require('../../static/table_man_icon.png'),
        },
        {
          name: '大屏报表',
          active: false,
          path: '/report/bigscreen',
          icon: require('../../static/screen_icon_dark.png'),
          actIcon: require('../../static/screen_icon.png'),
        },
        {
          name: '表格管理',
          active: false,
          path: '/report/excelreport',
          icon: require('../../static/table_man_icon_dark.png'),
          actIcon: require('../../static/table_man_icon.png'),
        },
        {
          name: '报表分享',
          active: false,
          path: '/report/shareManage',
          icon: require('../../static/share_icon_dark.png'),
          actIcon: require('../../static/share_icon.png'),
        },

      ]
    }
  },
  props: {
    activePath: {
      type: String,
      default: '/report/datasource'
    }
  },
  mounted() {
    this.menuClick(this.menus.find(menu => menu.path === this.activePath));
  },
  methods: {
    menuClick(menu) {
      if (menu.active) return;
      this.menus.forEach(item => {
        item.active = false;
      });
      menu.active = true;
      this.$router.push(menu.path);
    }
  }
}
</script>
<style lang="scss" scoped>
.menu {
  display: flex;
  flex-direction: column;
  width: 250px;
  background: #fff;
  border-radius: 8px;
  padding: 13px 8px;
  height: 930px;
  margin-right: 14px;
  flex-shrink: 0;
  .menu-item {
    position: relative;
    display: flex;
    align-items: center;
    margin-bottom: 30px;
    width: 100%;
    border-radius: 8px;
    padding: 10px 23px;
    &.active {
      background: rgba(237, 242, 255, 1);
    }
    .bar {
      position: absolute;
      left: 0;
      top: 50%;
      transform: translateY(-50%);
      width: 4px;
      height: 20px;
      background: rgba(29, 64, 175, 1);
      border-radius: 8px;
    }
    img {
      width: 20px;
      height: 20px;
    }
    span {
      font-size: 14px;
      color: rgba(53, 64, 79, 1);
      margin-left: 11px;
    }
  }
}
</style>

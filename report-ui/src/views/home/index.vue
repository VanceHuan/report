<template>
  <div class="index-container">
    <div class="table-wrap">
      <div class="head">
        <img class="icon" src="../../../static/table_head_icon.png" alt="">
        <span class="title">数据简报</span>
      </div>
      <div class="body">
        <div class="item" @click="currentPage = 'list'">
          <div class="top">总共大屏数量（个）</div>
          <div class="bottom"><span class="num">231</span><span>较上月</span> <span class="rate">-56%</span><img
              src="../../../static/red_down_icon.png" alt=""></div>
        </div>
        <div class="item" @click="currentPage = 'share'">
          <div class="top">分享大屏数量(个)</div>
          <div class="bottom"><span class="num">245</span><span>较上月</span> <span class="rate green">56%</span><img
              src="../../../static/green_up_icon.png" alt=""></div>
        </div>
      </div>
    </div>
    <div class="row">
      <div v-for="item in currentPage == 'list' ? list : listShare" :key="item.id" class="item-wrap">
        <div class="bg">
          <img class="bg-img" src="../../../static/def_gra.png"  />
          <div class="content">
            <div class="title">{{ item.reportName }}</div>
            <footer>
              {{ item.updateTime }}
              <div class="operation">
                <el-button icon="el-icon-share" class="view" type="text" @click="share(item)"
                  v-permission="'bigScreenManage:share'" />
                <el-button icon="el-icon-view" class="view" type="text" @click="viewDesign(item)"
                  v-permission="'bigScreenManage:view'" />
                <el-button icon="el-icon-edit" class="edit" type="text" @click="openDesign(item)"
                  v-permission="'bigScreenManage:design'" />
              </div>
            </footer>
          </div>
          <div class="info">
            <span>制作人：官方</span>
            <span>下载次数：{{ item.downloadCount || 0}}</span>
          </div>
        </div>
      </div>
    </div>

    <div class="block">
      <el-pagination :total="totalCount" :page-sizes="[8, 20, 50, 100]" :page-size="currentPage == 'list' ? params.pageSize : paramsShare.pageSize"
        :current-page="currentPage == 'list' ? params.pageNumber : paramsShare.pageNumber" layout="total, sizes, prev, pager, next, jumper"
        @size-change="handleSizeChange" @current-change="handleCurrentChange" />
    </div>
    <ShareConfig :visib="visibleForShareDialog" :reportCode="reportCodeForShareDialog"
      :reportName="reportNameForShareDialog" :reportType="reportTypeForShareDialog"
      @handleClose="visibleForShareDialog = false" />
  </div>
</template>

<script>
import { reportPageList } from "@/api/report";
import { reportShareList } from "@/api/reportShare";
import ShareConfig from "../bigscreenDesigner/share/shareConfig";
export default {
  name: "index1",
  data() {
    return {
      list: [],
      listShare: [],
      currentPage: 'list',
      totalCount: 0,
      totalPage: 0,
      visibleForShareDialog: false,
      reportCodeForShareDialog: "",
      reportNameForShareDialog: "",
      reportTypeForShareDialog: "",
      params: {
        reportCode: "",
        reportName: "",
        reportType: "report_screen",
        pageNumber: 1,
        pageSize: 8,
        order: "DESC",
        sort: "update_time"
      },
      paramsShare: {
        reportCode: "",
        reportName: "",
        reportType: "report_screen",
        pageNumber: 1,
        pageSize: 8,
      }
    };
  },
  components: { ShareConfig },
  watch: {
    currentPage(val) {
      if (val == 'list') {
        this.queryByPage();
      } else {
        this.getShareList();
      }
    }
  },
  created() {
    this.queryByPage();
  },
  mounted() {

  },
  methods: {
    async queryByPage() {
      const res = await reportPageList(this.params);
      if (res.code != "200") return;
      this.list = res.data.records;
      this.list.forEach(value => {
        value["reportNameCode"] =
          value.reportName + "[" + value.reportCode + "]";
      });
      this.totalCount = res.data.total;
      this.totalPage = res.data.pages;
      console.log(this.list);

    },
    async getShareList() {
      const res = await reportShareList(this.paramsShare);
      if (res.code != "200") return;
      this.listShare = res.data.records;
      this.totalCount = res.data.total;
      this.totalPage = res.data.pages;
      console.log(this.listShare);

    },
    search() {
      this.params.pageNumber = 1;
      this.queryByPage();
    },
    // 重置
    reset(formName) {
      this.$refs[formName].resetFields();
      this.params.reportName = "";
      this.params.reportCode = "";
      this.params.pageNumber = 1;
      this.queryByPage();
    },
    handleSizeChange(val) {
      this.params.pageSize = val;
      this.queryByPage();
    },
    handleCurrentChange(val) {
      this.params.pageNumber = val;
      this.queryByPage();
    },
    // 分享
    share(val) {
      this.reportCodeForShareDialog = val.reportCode;
      this.reportNameForShareDialog = val.reportName;
      this.reportTypeForShareDialog = val.reportType;
      this.visibleForShareDialog = true;
    },
    openDesign(val) {
      let routeUrl = this.$router.resolve({
        //path: "/screenDesigner",
        path: "/bigscreen/designer",
        query: {
          reportCode: val.reportCode
        }
      });
      window.open(routeUrl.href, "_blank");
    },
    viewDesign(val) {
      let routeUrl = this.$router.resolve({
        path: "/bigscreen/viewer",
        query: { reportCode: val.reportCode }
      });
      window.open(routeUrl.href, "_blank");
    }
  }
};
</script>

<style rel="stylesheet/scss" lang="scss">
.index-container {
  padding-top: 72px;
  padding-right: 34px;
  box-sizing: border-box;
  height: 100%;
  width: 100%;

  .table-wrap {
    position: relative;
    width: 100%;
    height: 179px;
    border-radius: 8px;
    background: #fff;
    padding: 66px 40px 33px 40px;

    .head {
      position: absolute;
      left: 19px;
      top: 19px;
      display: flex;
      align-items: center;

      .icon {
        width: 13px;
        height: 13px;
      }

      .title {
        font-size: 16px;
        color: rgba(53, 64, 79, 1);
        margin-left: 8px;
      }
    }

    .body {
      width: 100%;
      height: 100%;
      display: flex;
      flex-direction: row;

      .item {
        display: flex;
        flex-direction: column;
        box-shadow: 0px 0px 2px rgba(31, 45, 61, 0.3);
        border-radius: 8px;
        padding: 14px 24px;
        margin-right: 27px;
        cursor: pointer;

        .top {
          font-size: 14px;
          color: rgba(71, 86, 105, 1);
          margin-bottom: 8px;
        }

        .bottom {
          display: flex;
          align-items: flex-end;
          font-size: 12px;
          color: rgba(71, 86, 105, 1);

          .num {
            font-size: 22px;
            color: rgba(53, 64, 79, 1);
            margin-bottom: -3px;
          }

          .rate {
            color: rgba(250, 80, 80, 1);
            font-size: 10px;

            &.green {
              color: rgba(0, 186, 173, 1);
            }
          }

          img {
            width: 15px;
            height: 15px;
          }
        }
      }
    }
  }

  .row {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    width: 100%;

    .item-wrap {
      margin-right: 26px;

      &:nth-of-type(3n) {
        margin-right: 0;
      }
    }

    .bg {
      width: 390px;
      height: 244px;
      box-sizing: border-box;
      position: relative;
      overflow: hidden;
      margin: 10px 0;
      padding: 31px 18px 10px;
      background: #fff;

      .bg-img {
        position: absolute;
        left: 18px;
        top: 31px;
        width: 355px;
        height: 177px;
        z-index: 2;
      }
    }

    .content {
      width: 100%;
      height: 100%;
      position: absolute;
      color: #fff;
      left: 0;
      top: 0;
      z-index: 3;

      .title {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        font-size: 24px;
        font-weight: 500;
        color: #fff;
      }

      footer {
        width: 100%;
        font-size: 14px;
        padding: 14px 31px;
        line-height: 30px;
        position: absolute;
        z-index: 3;
        bottom: 36px;

        .operation {
          float: right;

          .view,
          .edit {
            color: #fff;
            font-size: 14px;
          }
        }
      }
    }

    .info {
      position: absolute;
      bottom: 10px;
      left: 0;
      display: flex;
      justify-content: space-between;
      width: 100%;
      box-sizing: border-box;
      padding: 0 18px;
      font-size: 10px;
      color: #CCCCCC;
    }
  }

  .block {
    position: fixed;
    bottom: 40px;
    right: 64px;
  }
}
</style>

<template>
  <div class="wrap">
    <div class="head">
      <img src="../../../static/table_sin_icon.png" alt="">
      <span>报表设计</span>
    </div>
    <div class="content">
      <Menu activePath="/report/bigscreen" />
      <div class="main-layout">
        <div class="head">
          <img src="../../../static/screen_icon.png" alt="">
          <span>大屏报表</span>
        </div>
        <el-form ref="form" :model="params" :rules="rules" label-width="120px">
          <!-- 搜索 -->
          <div class="form-box">
            <el-form-item label="报表名称">
              <el-input v-model="params.reportName" size="mini" clearable placeholder="名称" class="filter-item" />
            </el-form-item>
            <el-form-item label="报表编码">
              <el-input v-model="params.reportCode" size="mini" clearable placeholder="报表编码" class="filter-item" />
            </el-form-item>
            <el-button
              style="display: flex;justify-content: center;align-items: center; width: 50px;height: 31px;border-radius: 8px;border: 1px solid rgba(29, 64, 175, 1);color: rgba(71, 86, 105, 1);margin-right: 30px;"
              @click="search('form')">查询</el-button>
            <el-button
              style="display: flex;justify-content: center;align-items: center; width: 50px;height: 31px;border-radius: 8px;background: rgba(29, 64, 175, 1); color: #fff;"
              @click="reset('form')">重置</el-button>
          </div>
        </el-form>
        <div class="list">
          <div v-for="item in list" :key="item.id" class="list-item">
            <div class="bg">
              <img class="bg-img" src="../../../static/def_gra.png" />
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
            </div>
            <div class="info">
              <span>制作人：官方</span>
              <span>下载次数：223</span>
            </div>
          </div>
        </div>

        <div class="block">
          <el-pagination :total="totalCount" :page-sizes="[8, 20, 50, 100]" :page-size="params.pageSize"
            :current-page="params.pageNumber" layout="total, sizes, prev, pager, next, jumper"
            @size-change="handleSizeChange" @current-change="handleCurrentChange" />
        </div>
        <ShareConfig :visib="visibleForShareDialog" :reportCode="reportCodeForShareDialog"
          :reportName="reportNameForShareDialog" :reportType="reportTypeForShareDialog"
          @handleClose="visibleForShareDialog = false" />
      </div>
    </div>
  </div>

</template>

<script>
import ShareConfig from "./share/shareConfig";
import { reportPageList } from "@/api/report";
import Menu from "../../components/Menu.vue";
export default {
  name: "Login",
  components: { ShareConfig, Menu },
  data() {
    return {
      list: [],
      rules: {},
      totalCount: 0,
      totalPage: 0,
      params: {
        reportCode: "",
        reportName: "",
        reportType: "report_screen",
        pageNumber: 1,
        pageSize: 8,
        order: "DESC",
        sort: "update_time"
      },
      // 分享
      visibleForShareDialog: false,
      reportCodeForShareDialog: "",
      reportNameForShareDialog: "",
      reportTypeForShareDialog: "",
    };
  },
  mounted() { },
  created() {
    this.queryByPage();
  },
  methods: {
    // 查询
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
    async queryByPage() {
      const res = await reportPageList(this.params);
      if (res.code != "200") return;
      this.listLoading = true;
      this.list = res.data.records;
      this.list.forEach(value => {
        value["reportNameCode"] =
          value.reportName + "[" + value.reportCode + "]";
      });
      this.totalCount = res.data.total;
      this.totalPage = res.data.pages;
      this.listLoading = false;
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

<style scoped lang="scss">
.wrap {
  padding-top: 18px;

  .head {
    display: flex;
    align-items: center;
    margin-bottom: 26px;

    img {
      width: 24px;
      height: 24px;
    }

    span {
      color: rgba(29, 64, 175, 1);
      font-size: 14px;
      margin-left: 11px;
    }
  }

  .content {
    display: flex;
    flex-direction: row;
    flex-shrink: 0;
  }
}

.main-layout {
  padding: 20px;
  position: relative;
  height: auto;
  background: #fff;
  width: 70%;
  border-radius: 8px;

  .head {
    display: flex;
    align-items: center;
    margin-bottom: 10px;

    img {
      width: 20px;
      height: 20px;
    }

    span {
      color: rgba(53, 64, 79, 1);
      font-size: 16px;
      margin-left: 10px;
    }
  }

  .list {
    display: flex;
    flex-wrap: wrap;

    .list-item {
      margin-top: 31px;
      position: relative;
      .bg {
        width: 328px;
        height: 177px;
        box-sizing: border-box;
        position: relative;
        overflow: hidden;
        background: #fff;

        .bg-img {
          position: absolute;
          left: 0;
          top: 0;
          width: 328px;
          height: 177px;
          z-index: 2;
        }
      }
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
      position: absolute;
      z-index: 3;
      padding: 0 13px;
      bottom: 11px;
      display: flex;
      justify-content: space-between;

      .operation {
        display: flex;

        .view,
        .edit {
          color: #fff;
          font-size: 14px;
          padding: 0;
          margin-left: 30px;
        }
      }
    }
  }

  .info {
    position: absolute;
    bottom: -20px;
    left: 0;
    display: flex;
    justify-content: space-between;
    width: 100%;
    box-sizing: border-box;
    font-size: 10px;
    color: #CCCCCC;
  }
}

.block {
  position: absolute;
  right: 42px;
  bottom: 75px;
}

.form-box {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
}

/deep/ .el-form-item {
  margin-bottom: 0;
  display: flex;
  margin-right: 54px;

  .el-form-item__label {
    width: 70px !important;
    margin-right: 14px;
    display: flex;
    align-items: center;
  }

  .el-form-item__content {
    display: flex;
    align-items: center;
    margin-left: 0 !important;
    width: 244px;
    height: 42.28px;
    border-radius: 8px;
    background: rgba(255, 255, 255, 1);

    border: 1px solid rgba(210, 220, 231, 1);

    input {
      border: none;
    }
  }
}
</style>

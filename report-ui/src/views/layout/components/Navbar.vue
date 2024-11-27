<template>
  <div class="navbar">
    <div class="nav-item">
      <img src="../../../../static/home.png" />
      <span class="title">首页</span>
    </div>
    <div class="avatar-wrap">
      <img src="../../../../static/avatar.png" alt="头像" class="avatar"  />
      <img src="../../../../static/down_icon.png" alt="下拉" class="down-icon" />
    </div>
  </div>
</template>

<script>
import { reqUpdatePassword } from "@/api/login";
import { transPsw } from "@/utils/encrypted";

export default {
  data() {
    // 确认密码
    let validatePass3 = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value !== this.form.password) {
        callback(new Error("两次输入密码不一致!"));
      } else if (value.length < 6 || value.length > 20) {
        callback(new Error("密码长度需要再6-20之间!"));
      } else {
        callback();
      }
    };
    const validatePass = (rule, value, callback) => {
      if (
        !/^(?![a-zA-Z]+$)(?![A-Z0-9]+$)(?![A-Z\W_]+$)(?![a-z0-9]+$)(?![a-z\W_]+$)(?![0-9\W_]+$)[a-zA-Z0-9\W_]{6,}$/.test(
          value
        )
      ) {
        callback(new Error("请按要求输入密码"));
      } else {
        callback();
      }
    };
    const validateOldPass = (rule, value, callback) => {
      if (value.length < 6 || value.length > 30) {
        callback(new Error("请输入原密码"));
      } else {
        callback();
      }
    };
    return {
      wordVisible: false, //修改密码弹框
      form: {
        oldPassword: "",
        password: "",
        confirmPassword: ""
      },
      rules: {
        oldPassword: [
          { required: true, validator: validateOldPass, trigger: "blur" }
        ],
        password: [
          { required: true, message: "请选择新密码", trigger: "blur" }
        ],
        confirmPassword: [
          { required: true, validator: validatePass3, trigger: "blur" }
        ]
      },
    };
  },
  components: {
  },
  computed: {
  },
  methods: {
    logout() {
      this.$confirm("确定要退出吗", "温馨提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        sessionStorage.clear();
        localStorage.clear();
        this.$router.push("/login");
      });
    },
    // 修改密码
    updatePassword() {
      this.wordVisible = true;
      this.$nextTick(() => {
        this.$refs.form && this.$refs.form.resetFields();
      });
    },
    // 发送请求 确认修改
    confrimUpdate() {
      this.$refs.form.validate(async valid => {
        if (valid) {
          const { oldPassword, password, confirmPassword } = this.form;
          let data = {
            oldPassword: transPsw(oldPassword),
            password: transPsw(password),
            confirmPassword: transPsw(confirmPassword)
          };

          const { code } = await reqUpdatePassword(data);
          if (code != "200") return;
          this.wordVisible = false;
          this.$message.success("修改密码成功,请重新登录");
          sessionStorage.clear();
          localStorage.clear();
          this.$router.push("/login");
        } else {
          return false;
        }
      });
    },

  }
};
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.navbar {
  width: calc(100% - 263px);
  height: 60px;
  position: fixed;
  top: 0;
  left: 263px;
  padding-top: 30px;
  display: flex;
  align-items: center;

  .nav-item {
    width: 80px;
    display: flex;
    align-items: center;
    justify-content: center;
    padding-bottom: 12px;
    border-bottom: 3px solid #1D40AF;

    img {
      width: 13px;
      height: 13px;
    }

    .title {
      font-size: 14px;
      color: #1D40AF;
      margin-left: 12px;
    }
  }

  .avatar-wrap {
    display: flex;
    align-items: center;
    flex-direction: row;
    position: absolute;
    right: 39px;
    top: 34px;
    .avatar {
      width: 26px;
      height: 26px;
    }
    .down-icon {
      width: 12px;
      height: 12px;
      margin-left: 4;
    }
  }
}
</style>

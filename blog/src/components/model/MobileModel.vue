<template>
  <v-dialog v-model="mobileFlag" :fullscreen="isMobile" max-width="460">
    <v-card class="login-container" style="border-radius:4px">
      <v-icon class="float-right" @click="mobileFlag = false">
        mdi-close
      </v-icon>
      <div class="login-wrapper">
        <!-- 用户名 -->
        <v-text-field
          v-model="mobile"
          clearable
          label="手机号"
          placeholder="请输入您的手机号"
          @keyup.enter="register"
        />
        <!-- 验证码 -->
        <div class="mt-7 send-wrapper">
          <v-text-field
            v-model="code"
            label="验证码"
            maxlength="6"
            placeholder="请输入6位验证码"
            @keyup.enter="register"
          />
          <v-btn :disabled="flag" small text @click="sendCode">
            {{ codeMsg }}
          </v-btn>
        </div>
        <!-- 按钮 -->
        <v-btn
          block
          class="mt-7"
          color="blue"
          style="color:#fff"
          @click="saveUserMobile"
        >
          绑定
        </v-btn>
      </div>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  data: function() {
    return {
      mobile: this.$store.state.mobile,
      code: "",
      flag: true,
      codeMsg: "发送",
      time: 60,
      show: false
    };
  },
  methods: {
    sendCode() {
      const that = this;
      // eslint-disable-next-line no-undef
      var captcha = new TencentCaptcha(this.config.TENCENT_CAPTCHA, function(
        res
      ) {
        if (res.ret === 0) {
          //发送邮件
          that.countDown();
          that.axios
            .get("/api/users/code", {
              params: { username: that.email }
            })
            .then(({ data }) => {
              if (data.flag) {
                that.$toast({ type: "success", message: data.message });
              } else {
                that.$toast({ type: "error", message: data.message });
              }
            });
        }
      });
      // 显示验证码
      captcha.show();
    },
    countDown() {
      this.flag = true;
      this.timer = setInterval(() => {
        this.time--;
        this.codeMsg = this.time + "s";
        if (this.time <= 0) {
          clearInterval(this.timer);
          this.codeMsg = "发送";
          this.time = 60;
          this.flag = false;
        }
      }, 1000);
    },
    saveUserMobile() {
      // var reg = /^[A-Za-z0-9\u4e00-\u9fa5]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/;
      var reg = /^(13[0-9]|14[01456879]|15[0-35-9]|16[2567]|17[0-8]|18[0-9]|19[0-35-9])\d{8}$/;
      if (!reg.test(this.mobile)) {
        this.$toast({ type: "error", message: "手机号码格式不正确" });
        return false;
      }
      if (this.code.trim().length != 6) {
        this.$toast({ type: "error", message: "请输入6位验证码" });
        return false;
      }
      const user = {
        mobile: this.mobile,
        code: this.code
      };
      this.axios.post("/api/users/email", user).then(({ data }) => {
        if (data.flag) {
          this.$store.commit("saveEmail", this.mobile);
          this.mobile = "";
          this.code = "";
          this.$store.commit("closeModel");
          this.$toast({ type: "success", message: data.message });
        } else {
          this.$toast({ type: "error", message: data.message });
        }
      });
    }
  },
  computed: {
    mobileFlag: {
      set(value) {
        this.$store.state.mobileFlag = value;
      },
      get() {
        console.log(this.$store.state.mobileFlag);
        return this.$store.state.mobileFlag;
      }
    },
    isMobile() {
      const clientWidth = document.documentElement.clientWidth;
      if (clientWidth > 960) {
        return false;
      }
      return true;
    }
  },
  watch: {
    mobile(value) {
      var reg = /^(13[0-9]|14[01456879]|15[0-35-9]|16[2567]|17[0-8]|18[0-9]|19[0-35-9])\d{8}$/;;
      if (reg.test(value)) {
        this.flag = false;
      } else {
        this.flag = true;
      }
    }
  }
};
</script>

<style scoped>
@media (min-width: 760px) {
  .login-container {
    padding: 1rem;
    border-radius: 4px;
    height: 400px;
  }
}
</style>

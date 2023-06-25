<template>
  <v-footer v-if="!this.isMessage" absolute app padless>
    <div class="footer-wrap">
      <div>
        ©{{ blogInfo.websiteConfig.websiteCreateTime | year }} -
        {{ new Date().getFullYear() }} By
        {{ blogInfo.websiteConfig.websiteAuthor }}
      </div>
      <a href="https://beian.miit.gov.cn/" target="_blank">
        {{ blogInfo.websiteConfig.websiteRecordNo }}
      </a>
      <a
        style="display: block"
        href="https://www.beian.gov.cn/portal/registerSystemInfo?recordcode=11011202004437"
        target="_blank"
      >
        <!--src="https://www.beian.gov.cn/img/ghs.png"-->
        <img
          v-show="blogInfo.websiteConfig.websiteRGANo"
          alt="图标"
          src="https://static.yxin.top/upload/photos/d0289dc0a46fc5b15b3363ffa78cf6c7.png"
          style="position: relative;top: 4px;"
        />
        {{ blogInfo.websiteConfig.websiteRGANo }}
        <!--京公网安备 11011202004437号-->
      </a>
    </div>
  </v-footer>
</template>

<script>
export default {
  computed: {
    isMessage() {
      return this.$route.path == "/message";
    },
    blogInfo() {
      return this.$store.state.blogInfo;
    }
  }
};
</script>

<style scoped>
.footer-wrap {
  width: 100%;
  line-height: 2;
  position: relative;
  padding: 40px 20px;
  color: #eee;
  font-size: 14px;
  text-align: center;
  background: linear-gradient(-45deg, #ee7752, #ce3e75, #23a6d5, #23d5ab);
  background-size: 400% 400%;
  animation: Gradient 10s ease infinite;
}

.footer-wrap a {
  color: #eee !important;
}

@keyframes Gradient {
  0% {
    background-position: 0 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0 50%;
  }
}
</style>

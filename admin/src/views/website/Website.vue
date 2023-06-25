<template>
  <el-card class="main-card">
    <el-tabs v-model="activeName" @tab-click="handleClick">
      <!-- 修改信息 -->
      <el-tab-pane label="网站信息" name="info">
        <el-form
          :model="websiteConfigForm"
          label-position="left"
          label-width="100px"
        >
          <el-form-item label="网站头像">
            <button id="website-avatar" class="pick-avatar">
              <el-avatar
                  v-if="websiteConfigForm.websiteAvatar"
                  :size="120"
                  :src="websiteConfigForm.websiteAvatar"
                  class="avatar"
                  shape="square"
              />
            </button>
            <avatar-cropper
                trigger="#website-avatar"
                upload-url="/api/admin/config/images"
                @uploaded="handleWebsiteAvatarSuccess"
            />
          </el-form-item>
          <el-form-item label="网站名称">
            <el-input
              v-model="websiteConfigForm.websiteName"
              size="small"
              style="width:400px"
            />
          </el-form-item>
          <el-form-item label="网站作者">
            <el-input
              v-model="websiteConfigForm.websiteAuthor"
              size="small"
              style="width:400px"
            />
          </el-form-item>
          <el-form-item label="网站简介">
            <el-input
              v-model="websiteConfigForm.websiteIntro"
              size="small"
              style="width:400px"
            />
          </el-form-item>
          <el-form-item label="网站创建日期">
            <el-date-picker
              v-model="websiteConfigForm.websiteCreateTime"
              placeholder="选择日期"
              style="width:400px"
              type="date"
              value-format="yyyy-MM-dd"
            />
          </el-form-item>
          <el-form-item label="网站公告">
            <el-input
              v-model="websiteConfigForm.websiteNotice"
              :rows="5"
              placeholder="请输入公告内容"
              style="width:400px"
              type="textarea"
            />
          </el-form-item>
          <el-form-item label="备案号">
            <el-input
              v-model="websiteConfigForm.websiteRecordNo"
              size="small"
              style="width:400px"
            />
          </el-form-item>
          <el-form-item label="公网安备号">
            <el-input
                v-model="websiteConfigForm.websiteRGANo"
                size="small"
                style="width:400px"
            />
          </el-form-item>
          <el-form-item label="第三方登录">
            <el-checkbox-group v-model="websiteConfigForm.socialLoginList">
              <el-checkbox label="qq">QQ</el-checkbox>
              <el-checkbox label="weibo">微博</el-checkbox>
            </el-checkbox-group>
          </el-form-item>
          <el-button
            size="medium"
            style="margin-left:6.3rem"
            type="primary"
            @click="updateWebsiteConfig"
          >
            修改
          </el-button>
        </el-form>
      </el-tab-pane>
      <!-- 网站公告 -->
      <el-tab-pane label="社交信息" name="notice">
        <el-form :model="websiteConfigForm" label-width="70px">
          <el-checkbox-group v-model="websiteConfigForm.socialUrlList">
            <el-form-item label="QQ">
              <el-input
                v-model="websiteConfigForm.qq"
                size="small"
                style="width:400px;margin-right:1rem"
              />
              <el-checkbox label="qq">是否展示</el-checkbox>
            </el-form-item>
            <el-form-item label="Github">
              <el-input
                v-model="websiteConfigForm.github"
                size="small"
                style="width:400px;margin-right:1rem"
              />
              <el-checkbox label="github">是否展示</el-checkbox>
            </el-form-item>
            <el-form-item label="Gitee">
              <el-input
                v-model="websiteConfigForm.gitee"
                size="small"
                style="width:400px;margin-right:1rem"
              />
              <el-checkbox label="gitee">是否展示</el-checkbox>
            </el-form-item>
            <el-button
              size="medium"
              style="margin-left:4.375rem"
              type="primary"
              @click="updateWebsiteConfig"
            >
              修改
            </el-button>
          </el-checkbox-group>
        </el-form>
      </el-tab-pane>
      <!-- 修改密码 -->
      <el-tab-pane label="其他设置" name="password">
        <el-form
          :model="websiteConfigForm"
          label-position="left"
          label-width="120px"
        >
          <el-row style="width:600px">
            <el-col :md="12">
              <el-form-item label="用户头像">
                <button id="user-avatar" class="pick-avatar">
                  <el-avatar
                      v-if="websiteConfigForm.userAvatar"
                      :size="120"
                      :src="websiteConfigForm.userAvatar"
                      class="avatar"
                      shape="square"
                  />
                </button>
                <avatar-cropper
                    trigger="#user-avatar"
                    upload-url="/api/admin/config/images"
                    @uploaded="handleUserAvatarSuccess"
                />
              </el-form-item>
            </el-col>
            <el-col :md="12">
              <el-form-item label="游客头像">
                <button id="tourist-avatar" class="pick-avatar">
                  <el-avatar
                      v-if="websiteConfigForm.touristAvatar"
                      :size="120"
                      :src="websiteConfigForm.touristAvatar"
                      class="avatar"
                      shape="square"
                  />
                </button>
                <avatar-cropper
                    trigger="#tourist-avatar"
                    upload-url="/api/admin/config/images"
                    @uploaded="handleTouristAvatarSuccess"
                />
              </el-form-item>
            </el-col>
          </el-row>
          <el-form-item label="邮箱通知">
            <el-radio-group v-model="websiteConfigForm.isEmailNotice">
              <el-radio :label="0">关闭</el-radio>
              <el-radio :label="1">开启</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="评论审核">
            <el-radio-group v-model="websiteConfigForm.isCommentReview">
              <el-radio :label="0">关闭</el-radio>
              <el-radio :label="1">开启</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="留言审核">
            <el-radio-group v-model="websiteConfigForm.isMessageReview">
              <el-radio :label="0">关闭</el-radio>
              <el-radio :label="1">开启</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="打赏状态">
            <el-radio-group v-model="websiteConfigForm.isReward">
              <el-radio :label="0">关闭</el-radio>
              <el-radio :label="1">开启</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-row v-show="websiteConfigForm.isReward == 1" style="width:600px">
            <el-col :md="12">
              <el-form-item label="微信收款码">
                <el-upload
                  :on-success="handleWeiXinSuccess"
                  :show-file-list="false"
                  action="/api/admin/config/images"
                  class="avatar-uploader"
                >
                  <img
                    v-if="websiteConfigForm.weiXinQRCode"
                    :src="websiteConfigForm.weiXinQRCode"
                    class="avatar"
                  />
                  <i v-else class="el-icon-plus avatar-uploader-icon" />
                </el-upload>
              </el-form-item>
            </el-col>
            <el-col :md="12">
              <el-form-item label="支付宝收款码">
                <el-upload
                  :on-success="handleAlipaySuccess"
                  :show-file-list="false"
                  action="/api/admin/config/images"
                  class="avatar-uploader"
                >
                  <img
                    v-if="websiteConfigForm.alipayQRCode"
                    :src="websiteConfigForm.alipayQRCode"
                    class="avatar"
                  />
                  <i v-else class="el-icon-plus avatar-uploader-icon" />
                </el-upload>
              </el-form-item>
            </el-col>
          </el-row>
          <el-form-item label="聊天室状态">
            <el-radio-group v-model="websiteConfigForm.isChatRoom">
              <el-radio :label="0">关闭</el-radio>
              <el-radio :label="1">开启</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item
            v-show="websiteConfigForm.isChatRoom == 1"
            label="Websocket地址"
          >
            <el-input
              v-model="websiteConfigForm.websocketUrl"
              size="small"
              style="width:400px"
            />
          </el-form-item>
          <el-form-item label="音乐播放器状态">
            <el-radio-group v-model="websiteConfigForm.isMusicPlayer">
              <el-radio :label="0">关闭</el-radio>
              <el-radio :label="1">开启</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-button
            size="medium"
            style="margin-left:6.3rem"
            type="primary"
            @click="updateWebsiteConfig"
          >
            修改
          </el-button>
        </el-form>
      </el-tab-pane>
    </el-tabs>
  </el-card>
</template>

<script>
import AvatarCropper from "vue-avatar-cropper";

export default {
  components: {AvatarCropper},
  created() {
    this.getWebsiteConfig();
  },
  data: function () {
    return {
      websiteConfigForm: {
        websiteAvatar: "",
        websiteName: "",
        websiteAuthor: "",
        websiteIntro: "",
        websiteNotice: "",
        websiteCreateTime: null,
        websiteRecordNo: "",
        websiteRGANo: "",
        socialLoginList: [],
        socialUrlList: [],
        qq: "",
        github: "",
        gitee: "",
        userAvatar: "",
        touristAvatar: "",
        isReward: 1,
        weiXinQRCode: "",
        alipayQRCode: "",
        isChatRoom: 1,
        websocketUrl: "",
        isMusicPlayer: 1,
        isEmailNotice: 1,
        isCommentReview: 0,
        isMessageReview: 0
      },
      activeName: "info"
    };
  },
  methods: {
    getWebsiteConfig() {
      this.axios.get("/api/admin/website/config").then(({ data }) => {
        this.websiteConfigForm = data.data;
      });
    },
    handleClick(tab) {
      console.log(tab);
    },
    handleWebsiteAvatarSuccess(response) {
      this.websiteConfigForm.websiteAvatar = response.data;
    },
    handleUserAvatarSuccess(response) {
      this.websiteConfigForm.userAvatar = response.data;
    },
    handleTouristAvatarSuccess(response) {
      this.websiteConfigForm.touristAvatar = response.data;
    },
    handleWeiXinSuccess(response) {
      this.websiteConfigForm.weiXinQRCode = response.data;
    },
    handleAlipaySuccess(response) {
      this.websiteConfigForm.alipayQRCode = response.data;
    },
    updateWebsiteConfig() {
      this.axios
        .put("/api/admin/website/config", this.websiteConfigForm)
        .then(({ data }) => {
          if (data.flag) {
            this.$notify.success({
              title: "成功",
              message: data.message
            });
          } else {
            this.$notify.error({
              title: "失败",
              message: data.message
            });
          }
        });
    }
  }
};
</script>

<style>
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 120px;
  height: 120px;
  line-height: 120px;
  text-align: center;
}

.avatar {
  width: 120px;
  height: 120px;
  display: block;
}

.pick-avatar {
  outline: none;
  background-color: rgba(255, 255, 255, 0);
  border: 1px dashed #d9d9d9;
  padding: 0;
  cursor: pointer;
  border-radius: 6px;
}

.pick-avatar:hover {
  border-color: #409eff;
  border-radius: 6px;
}
</style>

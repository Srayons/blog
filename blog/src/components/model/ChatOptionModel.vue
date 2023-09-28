<template>
  <v-dialog
    v-model="isOpenChatModel"
    transition="dialog-top-transition"
    max-width="600"
    :persistent="true"
  >
    <!-- <template v-slot:activator="{ on, attrs }">
      <v-btn color="primary" v-bind="attrs" v-on="on">From the top</v-btn>
    </template> -->
    <template>
      <v-card>
        <v-toolbar color="primary" dark>公告</v-toolbar>
        <v-card-text>
          <div class="text-h2 pa-12"></div>
        </v-card-text>
        <v-card-actions class="justify-end">
          <v-btn
            class="upload-btn v-comment-btn"
            color="#fff"
            text
            @click="isCloseChatModel()"
            >已读</v-btn
          >
        </v-card-actions>
      </v-card>
    </template>
  </v-dialog>
</template>
<script>
export default {
  props: ["isShow"],
  data: function() {
    return {
      isOpenChatModel: false,
    };
  },
  created() {
    console.log(this.isShow);
    if (!this.isShow) {
      // 弹出公告弹窗
      this.isOpenChatModel = true;
    }
  },
  methods: {
    isCloseChatModel() {
      this.isOpenChatModel = false;
      // 记录IP地址
    },
  },
};
</script>

<template>
  <el-card class="main-card">
    <div class="title">{{ this.$route.name }}</div>
    <div class="review-menu">
      <span>状态</span>
      <span
        @click="changeReview(null)"
        :class="isReview == null ? 'active-review' : 'review'"
      >
        全部
      </span>
      <span
        @click="changeReview(1)"
        :class="isReview == 1 ? 'active-review' : 'review'"
      >
        正常
      </span>
      <span
        @click="changeReview(0)"
        :class="isReview == 0 ? 'active-review' : 'review'"
      >
        审核中
      </span>
    </div>
    <!-- 表格操作 -->
    <div class="operation-container">
      <el-button
        :disabled="messageIdList.length == 0"
        size="small"
        icon="el-icon-delete"
        type="danger"
        @click="deleteFlag = true"
      >
        批量删除
      </el-button>
      <el-button
        :disabled="messageIdList.length == 0"
        size="small"
        icon="el-icon-success"
        type="success"
        @click="updateMessageReview(null)"
      >
        批量通过
      </el-button>
      <!-- 数据筛选 -->
      <div style="margin-left:auto">
        <el-input
          v-model="keywords"
          prefix-icon="el-icon-search"
          size="small"
          placeholder="请输入用户昵称"
          style="width:200px"
          @keyup.enter.native="searchMessages"
        />
        <el-button
          icon="el-icon-search"
          size="small"
          type="primary"
          style="margin-left:1rem"
          @click="searchMessages"
        >
          搜索
        </el-button>
      </div>
    </div>
    <!-- 表格展示 -->
    <el-table
      border
      v-loading="loading"
      :data="messageList"
      @selection-change="selectionChange"
    >
      <!-- 表格列 -->
      <el-table-column type="selection" width="55" />
      <el-table-column align="center" label="头像" prop="avatar" width="150">
        <template slot-scope="scope">
          <img :src="scope.row.avatar" height="40" width="40" />
        </template>
      </el-table-column>
      <el-table-column
        prop="nickname"
        align="center"
        label="留言人"
        width="150"
      />
      <el-table-column align="center" label="留言内容" prop="messageContent" />
      <el-table-column
        align="center"
        label="ip地址"
        prop="ipAddress"
        width="150"
      />
      <el-table-column
        prop="ipSource"
        align="center"
        label="ip来源"
        width="170"
      />
      <!-- 状态 -->
      <el-table-column align="center" label="状态" prop="isReview" width="80">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.isReview == 0" type="warning">审核中</el-tag>
          <el-tag v-if="scope.row.isReview == 1" type="success">正常</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        label="留言时间"
        width="140"
        prop="createTime"
      >
        <template slot-scope="scope">
          <i class="el-icon-time" style="margin-right:5px" />
          {{ scope.row.createTime | date }}
        </template>
      </el-table-column>
      <!-- 列操作 -->
      <el-table-column align="center" label="操作" width="160">
        <template slot-scope="scope">
          <el-button
            v-if="scope.row.isReview == 0"
            size="mini"
            type="success"
            slot="reference"
            @click="updateMessageReview(scope.row.id)"
          >
            通过
          </el-button>
          <el-popconfirm
            style="margin-left:10px"
            title="确定删除吗？"
            @confirm="deleteMessage(scope.row.id)"
          >
            <el-button slot="reference" size="mini" type="danger">
              删除
            </el-button>
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-pagination
      :page-sizes="[10, 20]"
      background
      class="pagination-container"
      @size-change="sizeChange"
      :current-page="current"
      :page-size="size"
      :total="count"
      @current-change="currentChange"
      layout="total, sizes, prev, pager, next, jumper"
    />
    <!-- 批量删除对话框 -->
    <el-dialog :visible.sync="deleteFlag" width="30%">
      <div slot="title" class="dialog-title-container">
        <i class="el-icon-warning" style="color:#ff9900" />提示
      </div>
      <div style="font-size:1rem">是否删除选中项？</div>
      <div slot="footer">
        <el-button @click="deleteFlag = false">取 消</el-button>
        <el-button type="primary" @click="deleteMessage(null)">
          确 定
        </el-button>
      </div>
    </el-dialog>
  </el-card>
</template>

<script>
export default {
  created() {
    this.listMessages();
  },
  data: function() {
    return {
      loading: true,
      deleteFlag: false,
      messageIdList: [],
      messageList: [],
      keywords: null,
      isReview: null,
      current: 1,
      size: 10,
      count: 0
    };
  },
  methods: {
    selectionChange(messageList) {
      this.messageIdList = [];
      messageList.forEach(item => {
        this.messageIdList.push(item.id);
      });
    },
    searchMessages() {
      this.current = 1;
      this.listMessages();
    },
    sizeChange(size) {
      this.size = size;
      this.listMessages();
    },
    currentChange(current) {
      this.current = current;
      this.listMessages();
    },
    deleteMessage(id) {
      var param = {};
      if (id != null) {
        param = { data: [id] };
      } else {
        param = { data: this.messageIdList };
      }
      this.axios.delete("/api/admin/messages", param).then(({ data }) => {
        if (data.flag) {
          this.$notify.success({
            title: "成功",
            message: data.message
          });
          this.listMessages();
        } else {
          this.$notify.error({
            title: "失败",
            message: data.message
          });
        }
        this.deleteFlag = false;
      });
    },
    updateMessageReview(id) {
      let param = {};
      if (id != null) {
        param.idList = [id];
      } else {
        param.idList = this.messageIdList;
      }
      param.isReview = 1;
      this.axios.put("/api/admin/messages/review", param).then(({ data }) => {
        if (data.flag) {
          this.$notify.success({
            title: "成功",
            message: data.message
          });
          this.listMessages();
        } else {
          this.$notify.error({
            title: "失败",
            message: data.message
          });
        }
      });
    },
    changeReview(review) {
      this.isReview = review;
    },
    listMessages() {
      this.axios
        .get("/api/admin/messages", {
          params: {
            current: this.current,
            size: this.size,
            keywords: this.keywords,
            isReview: this.isReview
          }
        })
        .then(({ data }) => {
          this.messageList = data.data.recordList;
          this.count = data.data.count;
          this.loading = false;
        });
    }
  },
  watch: {
    isReview() {
      this.current = 1;
      this.listMessages();
    }
  }
};
</script>

<style scoped>
.operation-container {
  margin-top: 1.5rem;
}
.review-menu {
  font-size: 14px;
  margin-top: 40px;
  color: #999;
}
.review-menu span {
  margin-right: 24px;
}
.review {
  cursor: pointer;
}
.active-review {
  cursor: pointer;
  color: #333;
  font-weight: bold;
}
</style>

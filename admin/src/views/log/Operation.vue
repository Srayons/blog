<template>
  <el-card class="main-card">
    <!-- 标题 -->
    <div class="title">{{ this.$route.name }}</div>
    <div class="operation-container">
      <el-button
        type="danger"
        :disabled="this.logIdList.length == 0"
        icon="el-icon-delete"
        size="small"
        @click="isDelete = true"
      >
        批量删除
      </el-button>
      <!-- 数据筛选 -->
      <div style="margin-left:auto">
        <el-input
          v-model="keywords"
          prefix-icon="el-icon-search"
          size="small"
          placeholder="请输入模块名或描述"
          style="width:200px"
          @keyup.enter.native="searchLogs"
        />
        <el-button
          icon="el-icon-search"
          size="small"
          type="primary"
          style="margin-left:1rem"
          @click="searchLogs"
        >
          搜索
        </el-button>
      </div>
    </div>
    <!-- 权限列表 -->
    <el-table
      @selection-change="selectionChange"
      v-loading="loading"
      :data="logList"
    >
      <el-table-column align="center" type="selection" width="55" />
      <el-table-column
        align="center"
        label="系统模块"
        prop="optModule"
        width="120"
      />
      <el-table-column
        width="100"
        label="操作类型"
        prop="optType"
        align="center"
      />
      <el-table-column
        align="center"
        label="操作描述"
        prop="optDesc"
        width="150"
      />
      <el-table-column
        prop="requetMethod"
        align="center"
        label="请求方式"
        width="100"
      >
        <template v-if="scope.row.requestMethod" slot-scope="scope">
          <el-tag :type="tagType(scope.row.requestMethod)">
            {{ scope.row.requestMethod }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column align="center" label="操作人员" prop="nickname" />
      <el-table-column
        prop="ipAddress"
        align="center"
        label="登录ip"
        width="130"
      />
      <el-table-column
        align="center"
        label="登录地址"
        prop="ipSource"
        width="150"
      />
      <el-table-column
        align="center"
        label="操作日期"
        prop="createTime"
        width="190"
      >
        <template slot-scope="scope">
          <i class="el-icon-time" style="margin-right:5px" />
          {{ scope.row.createTime | dateTime }}
        </template>
      </el-table-column>
      <el-table-column align="center" label="操作" width="150">
        <template slot-scope="scope">
          <el-button
            size="mini"
            type="text"
            slot="reference"
            @click="check(scope.row)"
          >
            <i class="el-icon-view" /> 查看
          </el-button>
          <el-popconfirm
            title="确定删除吗？"
            style="margin-left:10px"
            @confirm="deleteLog(scope.row.id)"
          >
            <el-button slot="reference" size="mini" type="text">
              <i class="el-icon-delete" /> 删除
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
    <!-- 查看模态框 -->
    <el-dialog :visible.sync="isCheck" width="40%">
      <div slot="title" class="dialog-title-container">
        <i class="el-icon-more" />详细信息
      </div>

      <el-form ref="form" :model="optLog" label-width="100px" size="mini">
        <el-form-item label="操作模块：">
          {{ optLog.optModule }}
        </el-form-item>
        <el-form-item label="请求地址：">
          {{ optLog.optUrl }}
        </el-form-item>
        <el-form-item label="请求方式：">
          <el-tag :type="tagType(optLog.requestMethod)">
            {{ optLog.requestMethod }}
          </el-tag>
        </el-form-item>
        <el-form-item label="操作方法：">
          {{ optLog.optMethod }}
        </el-form-item>
        <el-form-item label="请求参数：">
          {{ optLog.requestParam }}
        </el-form-item>
        <el-form-item label="返回数据：">
          {{ optLog.responseData }}
        </el-form-item>
        <el-form-item label="操作人员：">
          {{ optLog.nickname }}
        </el-form-item>
      </el-form>
    </el-dialog>
    <!-- 批量删除对话框 -->
    <el-dialog :visible.sync="isDelete" width="30%">
      <div slot="title" class="dialog-title-container">
        <i class="el-icon-warning" style="color:#ff9900" />提示
      </div>
      <div style="font-size:1rem">是否删除选中项？</div>
      <div slot="footer">
        <el-button @click="isDelete = false">取 消</el-button>
        <el-button type="primary" @click="deleteLog(null)">
          确 定
        </el-button>
      </div>
    </el-dialog>
  </el-card>
</template>

<script>
export default {
  created() {
    this.listLogs();
  },
  data() {
    return {
      loading: true,
      logList: [],
      logIdList: [],
      keywords: null,
      current: 1,
      size: 10,
      count: 0,
      isCheck: false,
      isDelete: false,
      optLog: {}
    };
  },
  methods: {
    selectionChange(logList) {
      this.logIdList = [];
      logList.forEach(item => {
        this.logIdList.push(item.id);
      });
    },
    searchLogs() {
      this.current = 1;
      this.listLogs();
    },
    sizeChange(size) {
      this.size = size;
      this.listLogs();
    },
    currentChange(current) {
      this.current = current;
      this.listLogs();
    },
    listLogs() {
      this.axios
        .get("/api/admin/operation/logs", {
          params: {
            current: this.current,
            size: this.size,
            keywords: this.keywords
          }
        })
        .then(({ data }) => {
          this.logList = data.data.recordList;
          this.count = data.data.count;
          this.loading = false;
        });
    },
    deleteLog(id) {
      var param = {};
      if (id != null) {
        param = { data: [id] };
      } else {
        param = { data: this.logIdList };
      }
      this.axios.delete("/api/admin/operation/logs", param).then(({ data }) => {
        if (data.flag) {
          this.$notify.success({
            title: "成功",
            message: data.message
          });
          this.listLogs();
        } else {
          this.$notify.error({
            title: "失败",
            message: data.message
          });
        }
        this.isDelete = false;
      });
    },
    check(optLog) {
      this.optLog = JSON.parse(JSON.stringify(optLog));
      this.isCheck = true;
    }
  },
  computed: {
    tagType() {
      return function(type) {
        switch (type) {
          case "GET":
            return "";
          case "POST":
            return "success";
          case "PUT":
            return "warning";
          case "DELETE":
            return "danger";
        }
      };
    }
  }
};
</script>

<style scoped>
label {
  font-weight: bold !important;
}
</style>

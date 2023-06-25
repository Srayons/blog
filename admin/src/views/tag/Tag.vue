<template>
  <el-card class="main-card">
    <div class="title">{{ this.$route.name }}</div>
    <!-- 表格操作 -->
    <div class="operation-container">
      <el-button
        type="primary"
        icon="el-icon-plus"
        size="small"
        @click="openModel(null)"
      >
        新增
      </el-button>
      <el-button
        type="danger"
        :disabled="tagIdList.length == 0"
        icon="el-icon-delete"
        size="small"
        @click="isDelete = true"
      >
        批量删除
      </el-button>
      <div style="margin-left:auto">
        <el-input
          v-model="keywords"
          prefix-icon="el-icon-search"
          size="small"
          placeholder="请输入标签名"
          style="width:200px"
          @keyup.enter.native="searchTags"
        />
        <el-button
          icon="el-icon-search"
          size="small"
          type="primary"
          style="margin-left:1rem"
          @click="searchTags"
        >
          搜索
        </el-button>
      </div>
    </div>
    <!-- 表格展示 -->
    <el-table
      border
      v-loading="loading"
      :data="tagList"
      @selection-change="selectionChange"
    >
      <!-- 表格列 -->
      <el-table-column type="selection" width="55" />
      <!-- 标签名 -->
      <el-table-column align="center" label="标签名" prop="tagName">
        <template slot-scope="scope">
          <el-tag>
            {{ scope.row.tagName }}
          </el-tag>
        </template>
      </el-table-column>
      <!-- 文章量 -->
      <el-table-column align="center" label="文章量" prop="articleCount" />
      <!-- 标签创建时间 -->
      <el-table-column align="center" label="创建时间" prop="createTime">
        <template slot-scope="scope">
          <i class="el-icon-time" style="margin-right:5px" />
          {{ scope.row.createTime | date }}
        </template>
      </el-table-column>
      <!-- 列操作 -->
      <el-table-column align="center" label="操作" width="160">
        <template slot-scope="scope">
          <el-button size="mini" type="primary" @click="openModel(scope.row)">
            编辑
          </el-button>
          <el-popconfirm
            title="确定删除吗？"
            style="margin-left:1rem"
            @confirm="deleteTag(scope.row.id)"
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
    <el-dialog :visible.sync="isDelete" width="30%">
      <div slot="title" class="dialog-title-container">
        <i class="el-icon-warning" style="color:#ff9900" />提示
      </div>
      <div style="font-size:1rem">是否删除选中项？</div>
      <div slot="footer">
        <el-button @click="isDelete = false">取 消</el-button>
        <el-button type="primary" @click="deleteTag(null)">
          确 定
        </el-button>
      </div>
    </el-dialog>
    <!-- 编辑对话框 -->
    <el-dialog :visible.sync="addOrEdit" width="30%">
      <div ref="tagTitle" slot="title" class="dialog-title-container" />
      <el-form :model="tagForm" label-width="80px" size="medium">
        <el-form-item label="标签名">
          <el-input v-model="tagForm.tagName" style="width:220px" />
        </el-form-item>
      </el-form>
      <div slot="footer">
        <el-button @click="addOrEdit = false">取 消</el-button>
        <el-button type="primary" @click="addOrEditTag">
          确 定
        </el-button>
      </div>
    </el-dialog>
  </el-card>
</template>

<script>
export default {
  created() {
    this.listTags();
  },
  data: function() {
    return {
      isDelete: false,
      loading: true,
      addOrEdit: false,
      keywords: null,
      tagList: [],
      tagIdList: [],
      tagForm: {
        id: null,
        tagName: ""
      },
      current: 1,
      size: 10,
      count: 0
    };
  },
  methods: {
    selectionChange(tagList) {
      this.tagIdList = [];
      tagList.forEach(item => {
        this.tagIdList.push(item.id);
      });
    },
    searchTags() {
      this.current = 1;
      this.listTags();
    },
    sizeChange(size) {
      this.size = size;
      this.listTags();
    },
    currentChange(current) {
      this.current = current;
      this.listTags();
    },
    deleteTag(id) {
      var param = {};
      if (id == null) {
        param = { data: this.tagIdList };
      } else {
        param = { data: [id] };
      }
      this.axios.delete("/api/admin/tags", param).then(({ data }) => {
        if (data.flag) {
          this.$notify.success({
            title: "成功",
            message: data.message
          });
          this.listTags();
        } else {
          this.$notify.error({
            title: "失败",
            message: data.message
          });
        }
      });
      this.isDelete = false;
    },
    listTags() {
      this.axios
        .get("/api/admin/tags", {
          params: {
            current: this.current,
            size: this.size,
            keywords: this.keywords
          }
        })
        .then(({ data }) => {
          this.tagList = data.data.recordList;
          this.count = data.data.count;
          this.loading = false;
        });
    },
    openModel(tag) {
      if (tag != null) {
        this.tagForm = JSON.parse(JSON.stringify(tag));
        this.$refs.tagTitle.innerHTML = "修改标签";
      } else {
        this.tagForm.id = null;
        this.tagForm.tagName = "";
        this.$refs.tagTitle.innerHTML = "添加标签";
      }
      this.addOrEdit = true;
    },
    addOrEditTag() {
      if (this.tagForm.tagName.trim() == "") {
        this.$message.error("标签名不能为空");
        return false;
      }
      this.axios.post("/api/admin/tags", this.tagForm).then(({ data }) => {
        if (data.flag) {
          this.$notify.success({
            title: "成功",
            message: data.message
          });
          this.listTags();
        } else {
          this.$notify.error({
            title: "失败",
            message: data.message
          });
        }
      });
      this.addOrEdit = false;
    }
  }
};
</script>

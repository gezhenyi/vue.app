<template>
  <div class="app-container">
    <el-card class="box-card" style="margin-bottom:20px">
      <div style="display: flex">
          <img :src="detail.cover" style="width: 200px; height: 100px; margin-right: 30px;">
          <div style="flex: 1;">
                    <div style="display: flex;justify-content: space-between;">
                        <h4 style="margin-top: 5px;margin-bottom: 0;">{{detail.title}}</h4>
                        <small style="color: #bbbbbb;">{{ detail.isend ? '已完结' : '连载中' }}</small>
                    </div>
                    <p style="margin: 8px 0;">
                        <small style="color: #bbbbbb;">{{ detail.try }}</small>
                    </p>
                    <p>
                        <small style="color: red;">￥{{ detail.price }}</small>
                    </p>
                    <el-button-group >
                        <el-button size="small" type="warning" @click="changeDetailStatus">
                            {{ detail.status ? '下架' : '上架' }}
                        </el-button>
                        <el-button size="small" type="default" @click="changeDetailIsend">设为{{ detail.isend ? '连载中' : '已完结' }}</el-button>
                    </el-button-group>
                </div>
      </div>
    </el-card>
    <div class="filter-container">
      <el-button
        class="filter-item"
        style="margin-left: 10px"
        type="primary"
        icon="el-icon-edit"
        @click="handleCreate"
      >
        新增目录
      </el-button>
      <el-select
        v-model="listQuery.importance"
        placeholder="商品状态"
        clearable
        style="width: 90px; margin-left: 1100px"
        class="filter-item"
      >
        <el-option
          v-for="item in importanceOptions"
          :key="item"
          :label="item"
          :value="item"
        />
      </el-select>
      <el-input
        v-model="listQuery.title"
        placeholder="标题"
        style="width: 200px; margin-left: 20px"
        class="filter-item"
        @keyup.enter.native="handleFilter"
      />

      <el-button
        v-waves
        class="filter-item"
        type="primary"
        icon="el-icon-search"
        @click="handleFilter"
      >
        搜索
      </el-button>
    </div>

    <el-table
      :key="tableKey"
      v-loading="listLoading"
      :data="list"
      border
      fit
      highlight-current-row
      style="width: 100%"
      @sort-change="sortChange"
    >
      <el-table-column label="ID" sortable="custom" align="center" width="100">
        <template slot-scope="{ row }">
          <span>{{ row.id }}</span>
        </template>
      </el-table-column>
      <el-table-column align="left" label="单品内容" width="1100">
        <template slot-scope="scope">
          <div>
            <img
              style="float: left"
              :src="scope.row.cover"
              height="50px"
              width="100px"
              alt=""
            />
            <div>{{ scope.row.title }}</div>
            <div style="color: red">￥{{ scope.row.price }}</div>
          </div>
        </template>
      </el-table-column>

      <el-table-column align="center" label="订阅量" width="80">
        <template slot-scope="scope">
          <span>{{ scope.row.sub_count }}</span>
        </template>
      </el-table-column>

      <el-table-column
        align="center"
        label="状态"
        width="100"
        class-name="status-col"
      >
        <template slot-scope="scope">
          <el-tag :type="scope.row.status ? 'success' : 'danger'">
            {{ scope.row.status ? "已上架" : "已下架" }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column
        label="操作"
        align="center"
        class-name="small-padding fixed-width"
      >
        <template slot-scope="{ row, $index }">
          <el-button type="primary" size="mini" @click="handleUpdate(row)">
            编辑
          </el-button>
          <el-button
            v-if="row.status != 'deleted'"
            size="mini"
            type="danger"
            @click="handleDelete(row, $index)"
          >
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <pagination
      v-show="total > 0"
      :total="total"
      :page.sync="listQuery.page"
      :limit.sync="listQuery.limit"
      @pagination="getList"
    />

    <el-dialog
      lock-scroll=false
      :title="textMap[dialogStatus]"
      :visible.sync="dialogFormVisible"
     width="60%"
     
    >
     <el-tabs tab-position="left" style="height:500px;">
    <el-tab-pane label="图文" >
                      <el-table
                          :key="tableKey"
                          v-loading="listLoading"
                          :data="list"
                          border
                          fit
                          highlight-current-row
                          style="width: 100%"
                          @sort-change="sortChange"
                        >
                          <el-table-column
                            type="selection"
                            width="55">
                          </el-table-column>
                          <el-table-column align="left" label="内容" >
                            <template slot-scope="scope">
                              <div>
                                <img
                                  style="float: left "
                                  :src="scope.row.cover"
                                  height="50px"
                                  width="100px"
                                  alt=""
                                />
                                <div>  {{scope.row.title }}</div>
                                <div style="color: red">  ￥{{ scope.row.price }}</div>
                              </div>
                            </template>
                          </el-table-column>
                        </el-table>
    </el-tab-pane>
    <el-tab-pane label="音频">配置管理</el-tab-pane>
    <el-tab-pane label="视频">角色管理</el-tab-pane>
  </el-tabs>
   <pagination
      v-show="total > 0"
      :total="total"
      :page.sync="listQuery.page"
      :limit.sync="listQuery.limit"
      @pagination="getList"
    />
    </el-dialog>
  </div>
</template>

<script>
import {fetchList,fetchDetail, fetchDetailCourse,createColumn } from "@/api/column.js";
import waves from "@/directive/waves"; // waves directive
import { parseTime } from "@/utils";
import Pagination from "@/components/Pagination"; // secondary package based on el-pagination

export default {
  name: "ComplexTable",
  components: { Pagination },
  directives: { waves },
  filters: {
    statusFilter(status) {
      const statusMap = {
        published: "success",
        draft: "info",
        deleted: "danger",
      };
      return statusMap[status];
    },
    typeFilter(type) {
      return calendarTypeKeyValue[type];
    },
  },
  data() {
    return {
      detail: {
        content: "",
        cover: "",
        created_time: "",
        id: 0,
        isend: 0,
        price: 0,
        status: 1,
        sub_count: 0,
        title: "",
        try: "",
        updated_time: "",
      },
      tableKey: 0,
      list: null,
      total: 0,
      listLoading: true,
      listQuery: {
        page: 1,
        limit: 20,
        importance: undefined,
        title: undefined,
        type: undefined,
        sort: "+id",
      },
      importanceOptions: [1, 2, 3],
      sortOptions: [
        { label: "ID Ascending", key: "+id" },
        { label: "ID Descending", key: "-id" },
      ],
      statusOptions: ["published", "draft", "deleted"],
      showReviewer: false,
      temp: {
        title: "",
        try: "",
        content: "",
        t_price: 0,
        status: 0,
      },
      dialogFormVisible: false,
      dialogStatus: "",
      textMap: {
        update: "编辑",
        create: "选择课程",
      },
      dialogPvVisible: false,
      pvData: [],
      rules: {
        title: [
          { required: true, message: "title必须要填哦", trigger: "blur" },
        ],
        try: [{ required: true, message: "必须要填哦", trigger: "blur" }],
      },
      downloadLoading: false,
    };
  },
  created() {
    this.getList();
    this.getData();
    console.log(res);
  },
  methods: {
    getList() {
      this.listLoading = true;
      fetchList(this.listQuery).then((response) => {
        this.list = response.data.items;
        this.total = response.data.total;
        // Just to simulate the time of the request
        setTimeout(() => {
          this.listLoading = false;
        }, 1.5 * 1000);
      });
    },
    //目录切换状态
    changeDetailStatus(){
                this.detail.status = !this.detail.status 
            },
            changeDetailIsend(){
                this.detail.isend = !this.detail.isend 
            },
    getData() {
      fetchDetail({
        id: this.$route.query.id,
      }).then((res) => {
        console.log(res);
        this.detail = res.data;
      });
      console.log(res.data);
    },
    //目录跳转
    handmulu(row) {
      this.$router.push({
        path: "/course/column_detail",
        query: {
          id: row.id,
        },
      });
    },
    handleFilter() {
      this.listQuery.page = 1;
      this.getList();
    },
    handleModifyStatus(row, status) {
      this.$message({
        message: "操作成功",
        type: "success",
      });
      row.status = !status;
    },
    sortChange(data) {
      const { prop, order } = data;
      if (prop === "id") {
        this.sortByID(order);
      }
    },
    sortByID(order) {
      if (order === "ascending") {
        this.listQuery.sort = "+id";
      } else {
        this.listQuery.sort = "-id";
      }
      this.handleFilter();
    },
    resetTemp() {
      this.temp = {
        id: undefined,
        importance: 1,
        remark: "",
        timestamp: new Date(),
        title: "",
        status: "published",
        type: "",
      };
    },
    handleCreate() {
      this.resetTemp();
      this.dialogStatus = "create";
      this.dialogFormVisible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].clearValidate();
      });
    },
    createData() {
      this.$refs["dataForm"].validate((valid) => {
        if (valid) {
          this.temp.id = parseInt(Math.random() * 100) + 1024; // mock a id
          this.temp.author = "vue-element-admin";

          createMedia(this.temp).then(() => {
            console.log(222222222222222222222222);
            this.list.unshift(this.temp);
            this.dialogFormVisible = false;
            this.$notify({
              title: "Success",
              message: "Created Successfully",
              type: "success",
              duration: 2000,
            });
          });
        }
      });
    },
    handleUpdate(row) {
      this.temp = Object.assign({}, row); // copy obj
      this.temp.timestamp = new Date(this.temp.timestamp);
      this.dialogStatus = "update";
      this.dialogFormVisible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].clearValidate();
      });
    },
    updateData() {
      this.$refs["dataForm"].validate((valid) => {
        if (valid) {
          const tempData = Object.assign({}, this.temp);
          tempData.timestamp = +new Date(tempData.timestamp); // change Thu Nov 30 2017 16:41:05 GMT+0800 (CST) to 1512031311464
          updateArticle(tempData).then(() => {
            const index = this.list.findIndex((v) => v.id === this.temp.id);
            this.list.splice(index, 1, this.temp);
            this.dialogFormVisible = false;
            this.$notify({
              title: "Success",
              message: "Update Successfully",
              type: "success",
              duration: 2000,
            });
          });
        }
      });
    },
    handleDelete(row, index) {
      // deleteMedia
      this.$notify({
        title: "Success",
        message: "Delete Successfully",
        type: "success",
        duration: 2000,
      });
      this.list.splice(index, 1);
    },
    handleFetchPv(pv) {
      fetchPv(pv).then((response) => {
        this.pvData = response.data.pvData;
        this.dialogPvVisible = true;
      });
    },
    handleDownload() {
      this.downloadLoading = true;
      import("@/vendor/Export2Excel").then((excel) => {
        const tHeader = ["timestamp", "title", "type", "importance", "status"];
        const filterVal = [
          "timestamp",
          "title",
          "type",
          "importance",
          "status",
        ];
        const data = this.formatJson(filterVal);
        excel.export_json_to_excel({
          header: tHeader,
          data,
          filename: "table-list",
        });
        this.downloadLoading = false;
      });
    },
    formatJson(filterVal) {
      return this.list.map((v) =>
        filterVal.map((j) => {
          if (j === "timestamp") {
            return parseTime(v[j]);
          } else {
            return v[j];
          }
        })
      );
    },
    getSortClass: function (key) {
      const sort = this.listQuery.sort;
      return sort === `+${key}` ? "ascending" : "descending";
    },
  },
};
</script>

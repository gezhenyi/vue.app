<template name="component-name">
  <div>
    <div style="margin-top: 20px">
      <el-button
        class="btn-find"
        size="mini"
        type="primary"
        style="float: left; margin-left: 25px"
        @click="open"
        >新增图文</el-button
      >
      <el-select
        style="float: right; margin-right: 150px"
        size="mini"
        v-model="value"
        placeholder="商品状态"
      >
        <el-option
          v-for="item in tableData"
          :key="item.ID"
          :label="item.zhuangtai"
          :value="item.ID"
        >
        </el-option>
      </el-select>
      <el-input
        style="width: 10%; float: right; margin-right: 100px"
        size="mini"
        v-model="input"
        placeholder="标题"
      ></el-input>
      <el-button
        size="mini"
        type="primary"
        style="float: right; margin-right:10px"
        >搜索</el-button
      >
    </div>
    <div style="margin-left: 25; margin-top: 200">
      <el-table
        :data="tableData"
        height="250"
        border
        style="width: 100%; "
      >
        <el-table-column sortable prop="ID" label="ID" width="80">
        </el-table-column>
        <el-table-column prop="tuwen" label="图文内容" width="500">
        </el-table-column>
        <el-table-column prop="dingyue" label="订阅量" width="100">
        </el-table-column>
        <el-table-column
          prop="zhuangtai"
          label="状态"
          width="180"
          size="medium"
        >
        </el-table-column>
        <el-table-column prop="date" label="创建时间" width="180">
        </el-table-column>
        <el-table-column prop="address" label="操作">
          <template slot-scope="scope">
            <el-button
              size="mini"
              type="primary"
              @click="handleEdit(scope.$index, scope.row)"
              >编辑</el-button
            >
            <el-button
              size="mini"
              type="success"
              plain
              @click="handleDelete(scope.$index, scope.row)"
              >已上架</el-button
            >
            <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.$index, scope.row)"
              >删除</el-button
            >
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [
        {
          ID: 1,
          tuwen: "且叫天叫群",
          dingyue: "10",
          zhuangtai: "已上架",
          date: "2021-04-20 13:45:00",
        },{
          ID: 2,
          tuwen: "且叫天叫群",
          dingyue: "10",
          zhuangtai: "已上架",
          date: "2021-04-20 13:45:00",
        },{
          ID: 3,
          tuwen: "且叫天叫群",
          dingyue: "10",
          zhuangtai: "已上架",
          date: "2021-04-20 13:45:00",
        },{
          ID: 4,
          tuwen: "且叫天叫群",
          dingyue: "10",
          zhuangtai: "已下架",
          date: "2021-04-20 13:45:00",
        },{
          ID: 5,
          tuwen: "且叫天叫群",
          dingyue: "10",
          zhuangtai: "已下架",
          date: "2021-04-20 13:45:00",
        },
      ],

    };
  },
 
};
</script>

<style scoped>
</style>
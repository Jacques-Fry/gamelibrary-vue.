<template>
  <div class="LogContent">
    <LogTitle @searchDataList="searchDataList" :loading="loading" />

    <Table
      class="Table"
      :titles="titles"
      :columns="columns"
      :dataList="dataList"
      :option="{show:true,upd:false,del:true}"
    />

    <div class="paging">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="currentPage"
        :page-sizes="pageSizes"
        :page-size="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      ></el-pagination>
    </div>
  </div>
</template>
<script>
import LogTitle from "./LogTitle.vue";

import Table from "components/common/table/Table.vue";

import { queryLogList } from "network/log.js";

import { CommonDataListContent } from "common/mixins.js";

export default {
  name: "LogContent",
  data() {
    return {
      titles: [
        "操作",
        "操作用户",
        "IP",
        "操作结果",
        "花费时间(ms)",
        "操作时间",
      ],
      columns: ["name", "username", "ip", "result", "time", "createTime"],

      titles: [{ name: "操作" }, { name: "操作用户" },{ name: "IP" }, { name: "操作结果" },{ name: "花费时间(ms)" }, { name: "操作时间" }],
      columns: [
        { name: "name", styleEnable: false },
        { name: "username", styleEnable: false },
        { name: "ip", styleEnable: false },
        { name: "result", styleEnable: false },
        { name: "time", styleEnable: false },
        { name: "createTime", styleEnable: false },
      ],

      searchData: {
        name: "",
        username: "",
        ip: "",
      },
    };
  },
  components: { LogTitle, Table },
  created() {
    this.queryDataList();
  },
  methods: {
    // 分页查询操作日志
    queryDataList() {
      let loadingInstance = this.pageLoading();
      queryLogList(this.searchData, this.currentPage, this.pageSize).then(
        (res) => {
          this.pageStopLoading(loadingInstance);
          if (res && res.code === 200) {
            this.total = res.data.total;
            let list = res.data.data;
            list.forEach((item) => {
              item.createTime = this.timeFormat(item.createTime);
              item.isCheck = false;
            });
            this.dataList = list;
          }
        }
      );
    },
    searchDataList(data) {
      this.searchData = data;
      this.queryDataList();
    },
  },
  mixins: [CommonDataListContent],
};
</script>
<style scoped>
.LogContent {
  position: relative;
  height: 100%;
}
.Table {
  box-shadow: 0 0 2px 0 rgba(0, 0, 0, 0.2);
  position: relative;
  height: calc(100% - 138px);
}
.paging {
  text-align: center;
  margin-left: 10px;
  padding-top: 20px;

  width: 100%;
  height: 50px;
  background-color: #fff;
}
</style>
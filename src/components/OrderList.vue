<style scoped>
.load-more {
  /* font-size: 30px;*/
  font-weight: bold;
  color: #1da1f2;
  text-align: center;
  margin-bottom: 20px;
  border-radius: 10px;
  background-color: white;
  width: 100%;
}

.load-more:hover {
  cursor: pointer;
}
.no-more {
  width: 100%;
  text-align: center;
  margin-bottom: 20px;
  border-radius: 10px;
  font-weight: bold;
  color: #b9b9b9;
}
.el-header,
.el-footer {
  background-color: #b3c0d1;
  color: #337ab7;
  font-size: 30px;
  text-align: center;
  line-height: 60px;
}

.el-aside {
  background-color: #d3dce6;
  color: #333;
  text-align: center;
  line-height: 200px;
}

.el-main {
  background-color: #e9eef3;
  color: #333;
  text-align: center;
  line-height: 160px;
}

body > .el-container {
  margin-bottom: 40px;
}

.el-container:nth-child(5) .el-aside,
.el-container:nth-child(6) .el-aside {
  line-height: 260px;
}

.el-container:nth-child(7) .el-aside {
  line-height: 320px;
}
</style>

<template>
  <div>
    <el-container>
      <el-header>订单列表</el-header>

      <el-main>
        <el-row>
          <select v-model="year">
            <option value="%">请选择</option>
            <option v-for="item in years" v-bind:value="item">{{ item }}</option>
          </select>
          <span>年</span>
          <select v-model="month">
            <option value="%">请选择</option>
            <option v-for="item in months" v-bind:value="item">{{ item }}</option>
          </select>
          <span>月</span>
          <select v-model="day">
            <option value>请选择</option>
            <option v-for="item in days" v-bind:value="item">{{ item }}</option>
          </select>
          <span>日</span>
          <el-button @click="selectOrder()">筛选</el-button>
        </el-row>
        <el-table :data="orderTable" stripe style="width: 100%">
          <el-table-column prop="order_id" label="订单 ID" sortable min-width="100"></el-table-column>
          <el-table-column prop="movie_id" label="电影 ID" sortable min-width="160"></el-table-column>
          <el-table-column prop="movie_name" label="电影名称" sortable min-width="240"></el-table-column>
          <el-table-column prop="movie_num" label="购票数" sortable min-width="100"></el-table-column>
          <el-table-column prop="price_sum" label="总价" sortable min-width="160"></el-table-column>
          <el-table-column prop="create_time" label="创建时间" sortable min-width="200"></el-table-column>
        </el-table>
      </el-main>
    </el-container>
    <div v-if="has_more" class="load-more">
      <spin v-if="is_loading">
        <div>Loading...</div>
      </spin>
      <spin v-else>
        <div @click="generateData()">Load More...</div>
      </spin>
    </div>
    <div v-else class="no-more">No More</div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "OrderList",
  data() {
    return {
      orderTable: [],
      start_from: 0,
      limitation: 10,
      has_more: true,
      is_loading: false,
      scroll_top: 0,
      years: [],
      months: [],
      days: [],
      year: "%",
      day: "",
      month: "%",
      date_str: "%-%-%"
    };
  },
  mounted: function() {
    for (var i = 1; i <= 12; ++i) {
      if (i < 10) {
        this.months.push("0" + i);
      } else {
        this.months.push("" + i);
      }
    }
    for (var i = 1; i <= 31; ++i) {
      if (i < 10) {
        this.days.push("0" + i);
      } else {
        this.days.push("" + i);
      }
    }
    var date=new Date()
    for (var i = 2015; i <= date.getFullYear(); ++i) {
      this.years.push("" + i);
    }

    this.generateData();
  },
  methods: {
    generateData: function() {
      this.is_loading = true;
      this.scroll_top = document.documentElement.scrollTop;
      axios
        .post(`/api/query_order_list`, {
          start_from: this.start_from,
          limitation: this.limitation,
          time_limitation: this.date_str
        })
        .then(res => {
          this.start_from += this.limitation;
          if (res.data.length == 0) {
            this.has_more = false;
          } else {
            this.has_more = true;
          }
          this.orderTable = this.orderTable.concat(res.data);
          this.is_loading = false;
        });
    },
    selectOrder: function() {
      this.date_str = this.year + "-" + this.month + "-" + this.day + "%";
      console.log(this.date_str);
      this.orderTable = [];
      this.start_from = 0;
      this.generateData();
    }
  },
  updated: function() {
    this.$nextTick(function() {
      document.documentElement.scrollTop = this.scroll_top;
    });
  }
};
</script>


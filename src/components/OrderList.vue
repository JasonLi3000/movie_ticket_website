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
</style>

<template>
  <div>
    <el-container>
      <el-header >
        <div style="display:inline-block">订单列表</div>
        <div style="display:inline-block; text-align:right">
        <select model="year">
            <option value="">请选择</option>
            <option v-for="item in years" v-bind:value="item"> {{ item }}</option>
        </select>
        <span>年</span>
        <select model="month">
            <option value="%">请选择</option>
            <option v-for="item in months" v-bind:value="item"> {{ item }}</option>
        </select>
        <span>月</span>
        <select model="day">
            <option value="">请选择</option>
            <option v-for="item in days" v-bind:value="item"> {{ item }}</option>
        </select>
        <span>日</span>
        </div>
      </el-header>
      <el-main>
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
      years:[2015,2016,2017,2018,2019,2020],
      months:[],
      days:[2015,2016,2017,2018,2019,2020]
    };
  },
  mounted: function() {
    for(var i=1;i<=12;++i){
      this.months.push(i)
    }
    for(var i=1;i<=31;++i){
      this.days.push(i)
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
          limitation: this.limitation
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
    }
  },
  updated: function() {
    this.$nextTick(function() {
      document.documentElement.scrollTop=this.scroll_top
    });

  }
};
</script>

<style scoped>
</style>

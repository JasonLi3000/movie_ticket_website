<template>
  <div>
    <el-menu
      :default-active="onRoutes"
      mode="horizontal"
      @select="handleSelect"
      background-color="#545c64"
      text-color="#fff"
      active-text-color="#ffd04b"
      :router="true">
      <el-menu-item index="order-list" style="width: 20%; text-align: center;">订单查询</el-menu-item>
      <el-menu-item index="chart" style="width: 20%; text-align: center;">销售概况</el-menu-item>
      <el-button type="warning"
                 style="margin-top: 10px; margin-left: 48%; width: 10%;"
                 @click="goToIndex">管理员退出</el-button>
    </el-menu>
    <el-container>
      <el-main>
        <el-row>
          <div :id="id+0" v-bind:style="{height:height,width:width}"></div>
        </el-row>
        <el-row>
          <select v-model="now_year" @change="create_year_charts">
            <option v-for="item in years" v-bind:value="item">{{item}}</option>
          </select>
          <div :id="id+1" v-bind:style="{height:height,width:width}"></div>
        </el-row>
      </el-main>
    </el-container>
  </div>
</template>

<script>
  import echarts from "echarts";
  import axios from "axios";
  export default {
    name: "Chart",
    props: ["id", "height", "width"],
    data() {
      return {
        years: [],
        months: [
          "01",
          "02",
          "03",
          "04",
          "05",
          "06",
          "07",
          "08",
          "09",
          "10",
          "11",
          "12"
        ],
        orders: [],
        now_year: 2015,
        sale_values_one_year: {}
      };
    },
    mounted: function() {
      var date = new Date();
      for (var i = 2015; i <= date.getFullYear(); ++i) {
        this.years.push(i);
      }
      this.init();
      axios
        .post("/api/query_order_list", {
          start_from: 0,
          limitation: Number.MAX_SAFE_INTEGER
        })
        .then(res => {
          this.orders = res.data;

          for (var k = 0; k < this.years.length; ++k) {
            var tmp = [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0];
            for (var i = 0; i < this.orders.length; ++i) {
              for (var j = 0; j < 12; ++j) {
                if (
                  this.orders[i].create_time.indexOf(
                    this.years[k] + "-" + this.months[j]
                  ) != -1
                ) {
                  tmp[j] += this.orders[i].price_sum;
                  break;
                }
              }
            }
            this.sale_values_one_year[this.years[k]] = tmp;
          }

          this.create_pie_chart();
          this.create_year_charts();
        });
    },
    methods: {
      init: function() {
        var myChart = echarts.init(document.getElementById(this.id + "1"));
        // 绘制图表
        myChart.setOption({
          title: { text: "XXXX年销售情况" },
          tooltip: {},
          legend: {
            data: ["销售额"]
          },
          xAxis: {
            data: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
          },
          yAxis: {},
          series: [
            {
              name: "销售额",
              type: "bar",
              data: []
            }
          ]
        });
        myChart.showLoading();
        myChart = echarts.init(document.getElementById(this.id + "0"));
        // 绘制图表
        myChart.setOption({
          title: { text: "各年销售情况" },
          series: [
            {
              name: "销售额",
              type: "pie",
              data: []
            }
          ]
        });
        myChart.showLoading();
      },
      create_year_charts: function() {
        var max_v = 0;
        for (var i = 0; i < 12; ++i) {
          if (max_v < this.sale_values_one_year[this.now_year][i]) {
            max_v = this.sale_values_one_year[this.now_year][i];
          }
        }
        var y_values = [];
        for (var i = 0; i < 8; ++i) {
          y_values.push((max_v / 6) * i);
        }
        let myChart = echarts.init(document.getElementById(this.id + "1"));
        myChart.hideLoading();
        // 绘制图表
        myChart.setOption({
          title: { text: this.now_year + "销售情况" },
          tooltip: {},
          legend: {
            data: ["销售额"]
          },
          xAxis: {
            data: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
          },
          yAxis: {},
          series: [
            {
              name: "销售额",
              type: "bar",
              data: this.sale_values_one_year[this.now_year]
            }
          ]
        });
      },
      create_pie_chart: function() {
        var pie_data = [];
        for (var i = 0; i < this.years.length; ++i) {
          var value = 0;
          for (var j = 0; j < 12; ++j) {
            value += this.sale_values_one_year[this.years[i]][j];
          }
          pie_data.push({ name: this.years[i], value: value });
        }
        let myChart = echarts.init(document.getElementById(this.id + "0"));
        myChart.hideLoading();
        // 绘制图表
        myChart.setOption({
          title: { text: "各年销售情况" },
          tooltip: {
            formatter: function(params) {
              var str = "";
              str += "年份: " + params.name + "<br/>";
              str += "销售额: " + Number(params.value).toFixed(1);
              return str;
            }
          },
          series: [
            {
              name: "销售额",
              type: "pie",
              data: pie_data,
              label: {
                show: true,
                formatter: "{b} : ({d}%)"
              },
              labelLine: { show: true }
            }
          ]
        });
      },
      handleSelect(key, keyPath) {
        console.log(key, keyPath);
        // this.$router.push({name: key})
      },
      goToIndex() {
        this.$router.push({name: "index"})
      }
    },
    computed: {
      onRoutes () {
        return this.$route.path
      }
    }
  };
</script>

<template>
  <div>
    <el-container>
      <el-header>订单列表</el-header>
      <el-main>
        <el-table
          :data="orderTable"
          stripe
          style="width: 100%">
          <el-table-column
            prop="order_id"
            label="订单 ID"
            sortable
            width="160">
          </el-table-column>
          <el-table-column
            prop="movie_id"
            label="电影 ID"
            sortable
            width="160">
          </el-table-column>
          <el-table-column
            prop="movie_name"
            label="电影名称"
            sortable
            width="240">
          </el-table-column>
          <el-table-column
            prop="movie_num"
            label="购票数"
            sortable
            width="100">
          </el-table-column>
          <el-table-column
            prop="price_sum"
            label="总价"
            sortable
            width="120">
          </el-table-column>
          <el-table-column
            prop="create_time"
            label="创建时间"
            sortable>
          </el-table-column>
        </el-table>
      </el-main>
    </el-container>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: "OrderList",
    data() {
      return {
        orderTable: [
          {
            order_id: 53535,
            movie_id: 3123,
            movie_name: '好康的电影',
            movie_num: 2,
            price_sum: 88,
            create_time: "2015-08-07 03:15:07"
          },
          {
            order_id: 12,
            movie_id: 167,
            movie_name: '杰哥不要啊',
            movie_num: 3,
            price_sum: 64,
            create_time: "2015-09-07 06:58:49"
          }
        ]
      }
    },
    mounted: function() {
      axios.post(`/api/query_order_list`).then((res) => {
        this.orderTable = res.data;
      })
    }
  }
</script>

<style scoped>

</style>

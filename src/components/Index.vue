<template>
  <div>
    <el-container>
      <el-header>身份验证</el-header>
      <el-main>
        <el-button @click="goToMovieList">我是用户</el-button>
        <br />
        <el-button @click="goToOrderList">我是管理员</el-button>
      </el-main>
    </el-container>
  </div>
</template>

<script>
  export default {
    name: "OrderList",
    methods: {
      goToMovieList() {
        this.$router.push({name: "movie-list"})
      },
      goToOrderList() {
        this.$router.push({name: "order-list"})
      }
    }
  }
</script>

<style scoped>
  .el-header, .el-footer {
    background-color: #B3C0D1;
    color: #337ab7;
    font-size: 30px;
    text-align: center;
    line-height: 60px;
  }

  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    line-height: 200px;
  }

  .el-main {
    background-color: #E9EEF3;
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

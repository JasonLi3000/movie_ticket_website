<template>
  <div>
    <el-menu
      :default-active="'#'"
      mode="horizontal"
      @select="handleSelect"
      background-color="#545c64"
      text-color="#fff"
      active-text-color="#ffd04b"
      :router="true">
      <el-menu-item index="order-list" style="width: 20%; text-align: center;">订单查询</el-menu-item>
      <el-menu-item index="2" style="width: 20%; text-align: center;">订单分析</el-menu-item>
      <el-button type="warning"
                 style="margin-top: 10px; margin-left: 48%; width: 10%;"
                 @click="goToIndex">管理员退出</el-button>
    </el-menu>
  </div>
</template>

<script>
  export default {
    data() {
      return {

      };
    },
    methods: {
      handleSelect(key, keyPath) {
        console.log(key, keyPath);
        // this.$router.push({name: key})
      },
      goToIndex() {
        this.$router.push({name: "index"})
      }
    }
  }
</script>

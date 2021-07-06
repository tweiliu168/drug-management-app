<template>
  <el-container>
    <el-header style="font-family: Helvetica Neue; font-size: xxx-large">Drug Management System</el-header>
    <el-main>
      <el-row>
        <el-col :span="12">
          <DrugList />
        </el-col>
        <el-col :span="12">
          <DrugCabinet />
        </el-col>
      </el-row>
    </el-main>
  </el-container>
</template>

<script>

import DrugList from './components/DrugList.vue'
import DrugCabinet from './components/DrugCabinet.vue'

export default {
  name: 'App',
  components: {
    DrugList,
    DrugCabinet
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.el-table .cell {
  word-break: break-word !important;
}
</style>

<template>
  <div class="drug_list">
    <el-card :body-style="{ padding: '0px', height: '80vh' }" shadow="always" style="margin-right: 10px"> 
      <div style="padding: 10px">
        <el-input placeholder="Search" v-model="search" class="input-with-select">
          <template #append>
            <el-button icon="el-icon-search"></el-button>
          </template>
        </el-input>
      </div>
      <el-table
        :data="drugData.filter(data => !search || data.name.toLowerCase().includes(search.toLowerCase()))"
        style="width: 100%"
        max-height="700"
      >
        <el-table-column
          prop="name"
          label="Name"
        >
        </el-table-column>
        <el-table-column
          prop="side_effect"
          label="Side Effect"
        >
        </el-table-column>
        <el-table-column
          prop="effectiveness"
          label="Effectiveness"
        >
        </el-table-column>
        <el-table-column
          prop="comment"
          label="Comment"
        >
        </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
import mock_data from '../mock_data.json'

export default {
  name: 'DrugList',
  props: {},
  data() {
    return {
      drugData: mock_data,
      search: ''
    }
  }
}
</script>
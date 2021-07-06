<template>
  <div class="drug_cabinet">
    <el-card :body-style="{ padding: '0px', height: '80vh' }" shadow="always" style="margin-left: 10px">
      <CreateDrug :cabinetData="cabinetData" :id="id" @eventname="updateparent"></CreateDrug>
      <div style="font-size: x-large;padding:15px; text-align: left"><Strong>User Medicine Cabinet</Strong></div>
      <el-divider style="margin-top: 0px"></el-divider>
      <el-table
        :data="cabinetData"
        style="width: 100%"
        max-height="700"
      >
        <el-table-column
          prop="name"
          label="Name"
          :min-width="40"
        >
        </el-table-column>
        <el-table-column
          prop="side_effect"
          label="Side Effect"
          :min-width="50"
          sortable
        >
        </el-table-column>
        <el-table-column
          prop="effectiveness"
          label="Effectiveness"
          :min-width="55"
          sortable
        >
        </el-table-column>
        <el-table-column
          prop="comment"
          label="Comment"
        >
        </el-table-column>
        <el-table-column
          prop="operations"
          label="Operations"
          :min-width="60"
        >
          <template #default="scope">
            <el-button
              size="mini"
              @click="handleEdit(scope.$index, scope.row)"><i class="el-icon-edit"></i></el-button>
            <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.$index, scope.row)"><i class="el-icon-delete"></i></el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
    <el-dialog
      title="Edit Drug"
      v-model="showEditDialog"
      width="30%"
    >
      <el-form :model="editForm" :rules="rules" ref="editForm" label-width="120px" class="demo-ruleForm">
        <el-form-item label="Drug Name" prop="name">
          <el-select v-model="editForm.name" placeholder="Select" style="width: 100%" filterable>
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="Side Effects" prop="side_effect">
          <el-input-number style="width: 100%" v-model="editForm.side_effect" :step="0.1"></el-input-number>
        </el-form-item>
        <el-form-item label="Effectiveness" prop="effectiveness">
          <el-input-number style="width: 100%" v-model="editForm.effectiveness" :step="0.1"></el-input-number>
        </el-form-item>
        <el-form-item label="Comment" prop="comment">
          <el-input
            type="textarea"
            v-model="editForm.comment"
            placeholder="Please input"
            maxlength="100"
            show-word-limit
          ></el-input>
        </el-form-item>
      </el-form>
      <template #footer>
        <span class="dialog-footer">
          <el-button @click="showEditDialog = false">Cancel</el-button>
          <el-button type="primary" @click="edit('editForm')">Confirm</el-button>
        </span>
      </template>
    </el-dialog>
  </div>
</template>

<script>
import CreateDrug from './CreateDrug.vue'
import mock_data from '../mock_data.json'
export default {
  name: 'DrugCabinet',
  components: {
    CreateDrug
  },
  props: {},
  data() {
    return {
      showEditDialog: false,
      editingId: undefined,
      id: 0,
      cabinetData: [],
      options: [],
      rules: {
        name: [
          { required: true, message: 'Please input Drug Name', trigger: 'blur' },
          { message: 'Name cannot be empty', trigger: 'blur' }
        ],
        side_effect: [
          { required: true, type: 'number', min: 1, max: 5, message: 'Rating should be between 1 to 5', trigger: 'blur' }
        ],
        effectiveness: [
          { required: true, type: 'number', min: 1, max: 5, message: 'Rating should be between 1 to 5', trigger:'blur'},
        ],
        comment: [
          { required: true, message: 'Please input Comment', trigger: 'blur' }
        ]
      },
      editForm: {
        name: '',
        side_effect: 0,
        effectiveness: 0,
        comment: ''
      }
    }
  },
  created() {
    const options = mock_data.map(drug => {
      return {
        label: drug.name,
        value: drug.name
      }
    });
    this.options = options;
  },
  methods: {
    handleEdit(index, row) {
      this.showEditDialog = true;
      this.editingId = row.id;
      this.editForm.name = row.name;
      this.editForm.side_effect = row.side_effect;
      this.editForm.effectiveness = row.effectiveness;
      this.editForm.comment = row.comment;
    },
    handleDelete(index, row) {
      this.cabinetData.forEach( drug => {
        if (drug.id == row.id) {
          index = this.cabinetData.indexOf(drug);
        }
      })
      this.cabinetData.splice(index, 1);
    },
    edit(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.cabinetData.map( drug => {
            if(drug.id == this.editingId) {
              drug.name = this.editForm.name;
              drug.side_effect = this.editForm.side_effect;
              drug.effectiveness = this.editForm.effectiveness;
              drug.comment = this.editForm.comment;
            }
            return drug;
          })
          this.showEditDialog = false;
          this.$message.success('success')
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    },
    updateparent(variable) {
      this.cabinetData = variable.cabinetData;
      this.id = variable.id;
      console.log(this.cabinetData, this.id)
    }
  }
}
</script>
<template>
  <el-container>
    <el-header style="font-family: Helvetica Neue; font-size: xxx-large">Drug Management System</el-header>
    <el-main>
      <el-row>
        <el-col :span="12">
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
        </el-col>
        <el-col :span="12">
          <el-card :body-style="{ padding: '0px', height: '80vh' }" shadow="always" style="margin-left: 10px">
            <div style="float: right; padding: 10px">
              <el-button type="primary" size="medium" @click="resetFields()">Create</el-button>
            </div>
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
                :min-width="40"
                sortable
              >
              </el-table-column>
              <el-table-column
                prop="effectiveness"
                label="Effectiveness"
                min-width="40"
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
                :min-width="50"
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
        </el-col>
      </el-row>
      <el-dialog
        title="Add New Drug"
        v-model="showCreateDialog"
        width="30%"
      >
        <el-form :model="createForm" :rules="rules" ref="createForm" label-width="120px" class="demo-ruleForm">
          <el-form-item label="Drug Name" prop="name">
            <el-input v-model="createForm.name" placeholder="Please input drug name"></el-input>
          </el-form-item>
          <el-form-item label="Side Effects" prop="side_effect">
            <el-input-number style="width: 100%" v-model="createForm.side_effect" :step="0.1"></el-input-number>
          </el-form-item>
          <el-form-item label="Effectiveness" prop="effectiveness">
            <el-input-number style="width: 100%" v-model="createForm.effectiveness" :step="0.1"></el-input-number>
          </el-form-item>
          <el-form-item label="Comment" prop="comment">
            <el-input
              type="textarea"
              v-model="createForm.comment"
              placeholder="Please input"
              maxlength="100"
              show-word-limit
            ></el-input>
          </el-form-item>
        </el-form>
        <template #footer>
          <span class="dialog-footer">
            <el-button @click="showCreateDialog = false">Cancel</el-button>
            <el-button @click="resetForm('createForm')">Reset</el-button>
            <el-button type="primary" @click="create('createForm')">Confirm</el-button>
          </span>
        </template>
      </el-dialog>
      <el-dialog
        title="Edit Drug"
        v-model="showEditDialog"
        width="30%"
      >
        <el-form :model="editForm" :rules="rules" ref="editForm" label-width="120px" class="demo-ruleForm">
          <el-form-item label="Drug Name" prop="name">
            <el-input v-model="editForm.name" placeholder="Please input drug name"></el-input>
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
    </el-main>
  </el-container>
</template>

<script>

import mock_data from './mock_data.json'

export default {
  name: 'App',
  components: {},
  data() {
    return {
      showCreateDialog: false,
      showEditDialog: false,
      editingId: undefined,
      search: '',
      id: 0,
      drugData: mock_data,
      cabinetData: [],
      rules: {
        name: [
          { required: true, message: 'Please input Drug Name', trigger: 'blur' },
          { message: 'Name cannot be empty', trigger: 'blur' }
        ],
        side_effect: [
          { required: true, type: 'number', min: 1, max: 5, message: 'Side Effect Rating should be between 1 to 5', trigger: 'blur' }
        ],
        effectiveness: [
          { required: true, type: 'number', min: 1, max: 5, message: 'Effectiveness Rating should be between 1 to 5', trigger:'blur'},
        ],
        comment: [
          { required: true, message: 'Please input Comment', trigger: 'blur' }
        ]
      },
      createForm: {
        name: '',
        side_effect: undefined,
        effectiveness: undefined,
        comment: ''
      },
      editForm: {
        name: '',
        side_effect: 0,
        effectiveness: 0,
        comment: ''
      }
    }
  },
  created() {},
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
    create(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.createForm.id = this.id;
          this.cabinetData.push(this.createForm);
          this.showCreateDialog = false;
          this.id += 1;
          this.$message.success('success');
        } else {
          console.log('error submit!!');
          return false;
        }
      });
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
    resetFields() {
      this.showCreateDialog = true;
      this.createForm = {
        name: '',
        side_effect: undefined,
        effectiveness: undefined,
        comment: ''
      }
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
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

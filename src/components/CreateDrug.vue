<template>
  <div class="create_drug">
    <div style="float: right; padding: 10px">
      <el-button type="primary" size="medium" @click="resetFields()">Create</el-button>
    </div>
    <el-dialog
      title="Add New Drug"
      v-model="showCreateDialog"
      width="30%"
    >
      <el-form :model="createForm" :rules="rules" ref="createForm" label-width="120px" class="demo-ruleForm">
        <el-form-item label="Drug Name" prop="name">
          <!-- <el-input v-model="createForm.name" placeholder="Please input drug name"></el-input> -->
          <el-select v-model="createForm.name" placeholder="Select" style="width: 100%" filterable>
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
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
  </div>
</template>

<script>
import mock_data from '../mock_data.json'

export default {
  name: 'CreateDrug',
  props: {
    id: Number,
    cabinetData: Array
  },
  data() {
    return {
      showCreateDialog: false,
      updatedId: 0,
      updatedCabinetData: [],
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
      createForm: {
        name: '',
        side_effect: undefined,
        effectiveness: undefined,
        comment: ''
      },
    }
  },
  created() {
    this.updatedId = this.id;
    this.updatedCabinetData = this.cabinetData;
    const options = mock_data.map(drug => {
      return {
        label: drug.name,
        value: drug.name
      }
    });
    this.options = options;
  },
  methods: {
    create(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.createForm.id = this.id;
          this.updatedCabinetData.push(this.createForm);
          this.showCreateDialog = false;
          this.updatedId += 1;
          this.$message.success('success');
          const update = {
            id: this.updatedId,
            cabinetData: this.updatedCabinetData
          }
          this.$emit('eventname', update)
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
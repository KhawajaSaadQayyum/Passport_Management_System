

<template>
  <div>
    <el-button type="primary" @click="showAddForm()">Add Record</el-button>
    <el-button type="primary" @click="exportToCSV">Export CSV</el-button>
    <el-table :data="employees"  border style="width: 100%"  >
   
      <el-table-column prop="firstName" label="First Name" width="90" />
      <el-table-column prop="lastName" label="Last Name" width="90" />
      <el-table-column prop="dateOfBirth" label="Date Of birth" width="110" />
      
      <el-table-column prop="companyName" label="Company Name" width="100" />
     
      <el-table-column prop="passportIssueDate" label="passort issue Date" width="120" />
      <el-table-column prop="passportExpiryDate" label="passort Expiry Date" width="120" />

      <el-table-column prop="email" label="Email" width="90" />
      <el-table-column prop="nationality" label="Nationality" width="100" />
      <el-table-column fixed="right" label="Operations" width="120">
        <template #default="scope">
          <el-button link type="primary" @click="deleteEmployee(scope.row.id)">Delete</el-button>
          <el-button link type="primary" @click="showEditForm(scope.row.id)">Edit</el-button>
        </template>
      </el-table-column>
    </el-table>

  </div>
  <!-- Add Form Dialog -->
  <div class="addform" v-if="addFormDialog">
    <el-dialog v-model="addFormDialog" title="Add Employee Record" width="500">
      <el-form :model="form">
        <el-form-item label="First Name" :label-width="formLabelWidth">
          <el-input v-model="addForm.firstName" autocomplete="off" />
        </el-form-item>
        <el-form-item label="Last Name" :label-width="formLabelWidth">
          <el-input v-model="addForm.lastName" autocomplete="off" />
        </el-form-item>
      
        <el-form-item label="Nationality" :label-width="formLabelWidth">
          <el-input v-model="addForm.nationality" autocomplete="off" />
        </el-form-item>
      
    <el-form-item label="Company Name">
      <el-select v-model="addForm.companyName" placeholder="please select your Company Name">
        <el-option label="A" value="A" />
        <el-option label="B" value="B" />
      </el-select>
    </el-form-item>
    <el-form-item label="Date Of Birth">
    <el-date-picker
          v-model="addForm.dateOfBirth"
          type="date"
          format="yyyy/MM/dd"
          placeholder="Expirt Date"
          style="width: 100%"
        />
      </el-form-item>
        <el-form-item label="Email" :label-width="formLabelWidth">
          <el-input v-model="addForm.email" autocomplete="off" />
        </el-form-item>
        <!-- Date Selection -->
        <el-form-item label="Passport Date">
      <el-col :span="11">
        <el-date-picker
          v-model="addForm.passportIssueDate"
          type="date"
          format="yyyy/MM/dd"
          placeholder="Issue Date"
          style="width: 100%"
        />
      </el-col>
      <el-col :span="2" class="text-center">
        <span class="text-gray-500">-</span>
      </el-col>
      <el-col :span="11">
        <el-date-picker
          v-model="addForm.passportExpiryDate"
          type="date"
          format="yyyy/MM/dd"
          placeholder="Expirt Date"
          style="width: 100%"
        />
      </el-col>
    </el-form-item>
        <el-form-item>
          <template #default="scope">
            <div class="dialog-footer">
              <el-button @click="cancleFormDialog()">Cancel</el-button>
              <el-button @click="addEmployee()">Confirm</el-button>
            </div>
          </template>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
  <!-- Edit Form Dialog -->
  <div class="Editform" v-if="editFormDialog">
    <el-dialog v-model="editFormDialog" title="Edit Employee Record" width="500">
      <el-form :model="form">
        <el-form-item label="First Name" :label-width="formLabelWidth">
          <el-input v-model="editEmployeeForm.firstName" autocomplete="off" />
        </el-form-item>
        <el-form-item label="Last Name" :label-width="formLabelWidth">
          <el-input v-model="editEmployeeForm.lastName" autocomplete="off" />
        </el-form-item>
        <el-form-item label="Email" :label-width="formLabelWidth">
          <el-input v-model="editEmployeeForm.email" autocomplete="off" />
        </el-form-item>
        <el-form-item label="Nationality" :label-width="formLabelWidth">
          <el-input v-model="editEmployeeForm.nationality" autocomplete="off" />
        </el-form-item>
      
    <el-form-item label="Company Name">
      <el-select v-model="editEmployeeForm.companyName" placeholder="please select your Company Name">
        <el-option label="A" value="A" />
        <el-option label="B" value="B" />
      </el-select>
    </el-form-item>
    <el-form-item label="Date Of Birth">
    <el-date-picker
          v-model="editEmployeeForm.dateOfBirth"
          type="date"
          format="yyyy/MM/dd"
          placeholder="Expirt Date"
          style="width: 100%"
        />
      </el-form-item>
        <!-- Date Selection -->
        <el-form-item label="Passport Date">
      <el-col :span="11">
        <el-date-picker
          v-model="editEmployeeForm.passportIssueDate"
          type="date"
          format="yyyy/MM/dd"
          placeholder="Issue Date"
          style="width: 100%"
        />
      </el-col>
      <el-col :span="2" class="text-center">
        <span class="text-gray-500">-</span>
      </el-col>
      <el-col :span="11">
        <el-date-picker
          v-model="editEmployeeForm.passportExpiryDate"
          type="date"
          format="yyyy/MM/dd"
          placeholder="Expirt Date"
          style="width: 100%"
        />
      </el-col>
    </el-form-item>
        <el-form-item>
          <template #default>

            <el-button @click="cancleFormDialog()">Cancel</el-button>
            <el-button link type="primary" @click="editEmployee()">Confirm</el-button>

          </template>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
import axios from 'axios';
import { h } from 'vue'
import { ElMessage } from 'element-plus'

export default {
  data() {
    return {


      addForm: {
        firstName: "",
        lastName: "",
        email: "",
        dateOfBirth:"".substring(0,10),

        nationality:"",
        companyName:"",
        passportIssueDate:"".substring(0,10),
        passportExpiryDate:"".substring(0,10)
      },
      editEmployeeForm: {
        id: "",
        firstName: "",
        lastName: "",
        email: "",
        dateOfBirth:"".substring(0,10),
        nationality:"",
        companyName:"",
        passportIssueDate:"".substring(0,10),
        passportExpiryDate:"".substring(0,10)
      },
      employees: [],
      addFormDialog: false,
      editFormDialog: false
    };
  },
  mounted() {
    this.fetchEmployees();
  },
  methods: {
    exportToCSV() {
      const csvContent = "data:text/csv;charset=utf-8,"
        + this.employees.map(row => Object.values(row).join(",")).join("\n");
      const encodedUri = encodeURI(csvContent);
      const link = document.createElement("a");
      link.setAttribute("href", encodedUri);
      link.setAttribute("download", "table_data.csv");
      document.body.appendChild(link);
      link.click();
    },
    fetchEmployees() {
      axios.get('http://localhost:8080/employees/get')
        .then(res => {
          this.employees = res.data;
        })
        .catch(error => {
          console.error('Error fetching employees:');
        });
    },
    addEmployee() {
      const addForm = {
        firstName: this.addForm.firstName,
        lastName: this.addForm.lastName,
        email: this.addForm.email,
        dateOfBirth:this.addForm.dateOfBirth,
        nationality:this.addForm.nationality,
        companyName:this.addForm.companyName,
        passportIssueDate:this.addForm.passportIssueDate,
        passportExpiryDate:this.addForm.passportExpiryDate,
      };

      axios.post('http://localhost:8080/employees/add', addForm)
        .then(response => {
          ElMessage.success('Record added successfully.')
          this.addFormDialog = false
          this.fetchEmployees();
          this.addForm.firstName="",
          this.addForm.lastName="",
          this.addForm.email=""
        })
        .catch(error => {
          ElMessage.warning('Record not inserted.')

          this.addFormDialog = false
        });

    },
    cancleFormDialog() {
      this.addFormDialog = false;
      this.editFormDialog = false;
      ElMessage.success('Ok Cancled!.')
    },

    deleteEmployee(id) {
      axios.delete(`http://localhost:8080/employees/delete/${id}`).then(res => {
        ElMessage.success("Deleted Successfully")
        this.fetchEmployees();
      }).catch(err => {
        ElMessage.error("Record Not Deleted something went wrong")
        this.fetchEmployees();
      })
    },
    editEmployee() {

      const editEmployee = {
        firstName: this.editEmployeeForm.firstName,
        lastName: this.editEmployeeForm.lastName,
        email: this.editEmployeeForm.email,
        dateOfBirth :this.editEmployeeForm.dateOfBirth,
        nationality : this.editEmployeeForm.nationality,
        companyName: this.editEmployeeForm.companyName,
        passportIssueDate: this.editEmployeeForm.passportIssueDate,
        passportExpiryDate : this.editEmployeeForm.passportExpiryDate
      };
      this.editFormDialog = false;

      axios.put(`http://localhost:8080/employees/update/${this.editEmployeeForm.id}`, editEmployee)
        .then(response => {
          ElMessage.success('Record edited successfully.')
          this.fetchEmployees();
          this.editFormDialog = false;
          this.editEmployeeForm.firstName=""
          this.editEmployeeForm.lastName=""
          this.editEmployeeForm.email=""
          this.editEmployeeForm.dateOfBirth="",
          this.editEmployeeForm.nationality="",
          this.editEmployeeForm.companyName="",
          this.editEmployeeForm.passportIssueDate=""
          this.editEmployeeForm.passportExpiryDate=""
         

        })
        .catch(error => {
          ElMessage.warning('Record not edit successfully.')
          this.editFormDialog = false;

        });

    },
    showAddForm() {
      this.addFormDialog = true;
    },
    showEditForm(id) {
      this.editEmployeeForm.id = id;
      this.editFormDialog = true;
    },
  }
};
</script>

<style scoped>
.form {}
</style>
















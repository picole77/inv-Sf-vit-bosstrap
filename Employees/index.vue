<script setup>
import axios from 'axios';
import {ref,onMounted, nextTick} from 'vue';
import {confirmation, sendRequest} from '../../functions';
import {useAuthStore} from '../../stores/auth';
import Modal from '../../components/Modal.vue';
import SelectImput from '../../components/SelectInput.vue';
import Paginate from 'vuejs-paginate-next';

const authStore = useAuthStore();
axios.defaults.headers.common['Authorization'] =  'Bearer'+authStore.authToken;

onMounted( ()=>{ getDepartments(), getEmployess(1) });
const departments = ref([]);
const employees = ref([]);
const load = ref(false);
const rows = ref(0);
const form = ref({
    name:'', email:'', phone:'',department_id:''
});
const title =ref('');
const nameInput = ref('');
const operation =ref('');
const id = ref('');
const close = ref([]);

const getDepartments = async () =>{
    await axios.get('/api/departments') .then(
        response =>(departments.value = response.data));
        load.value =true;
}
const getEmployees = async (page) =>{
    await axios.get('/api/employees?page='+page) .then(
            response =>(employees.value = response.data,
            rows.value =response.data.last_page
            ));
        load.value =true;
}
const deleteDepartment = (id,name)=>{
    confirmation(name,('/api/employees/'+id),'/employees');
}
const openModal = (op,name,email,phone,department,employyee) =>{
    clear();
    setTimeout( () => nextTick( () => nameInput.value.focus()),600);
    operation.value = op;
    id.value = employee;
    if(op ==1){
        title.value = 'create employee';
    }else{
        title.value = 'Update employee';
        form.value.name = name;
        form.value.email = email;
        form.value.phone = phone;
        form.value.department_id = department;
    }
 }
 const clear = () =>{
   
        form.value.name = '';
        form.value.email = '';
        form.value.phone = '';
        form.value.department_id = '';
 }
 const save = async () => {
    let res;
    if(operation.value ==1){
        res = await sendRequest('POST',form.value,'/api/employees','');
        if(res==true){
            clear();
            nextTick ( ()=> nameInput.value.focus());
            getEmployees(1);
        }else{
            res = await sendRequest('PuT',form.value,('/api/employees/'+id.value),'');
        if(res==true){
            nextTick ( ()=> close.value.click());
            getEmployees(1);
        }
    }
 }
</script>


<template>
    <div class="row">   
        <div class="col-md-4 offset-md-4">
            <div class="d-grid col-10 mx-auto">
                <button class="btn btn-dark" data-bs-toggle="modal"
                data-bs-target="#modal" @click="$event => openModal(1)">
                    <i class="fa-solid fa-circle-plus"></i> add
                </button>
            </div>     
        </div>
    </div>
    
    <div class="row mt-3">
        <div class="col-md-10 offset-md-1">
            <div class="card border border-white text-center" v-if="!load">
                <div class="card-body">
                    <img src="/cargando.gif" alt="img-fluid">
                </div>
            </div>
            <div class="table-responsive" v-else>
                <table class="table table-border table-hover">
                    <thead><tr><th>#</th><th>Nombre</th><th>Email</th><th>Phone</th>Departamento</tr></thead>
                    <tbody class="table-group-divider">
                        <tr v-for="emp,i in employees.data" :key="emp.id">
                            <td>{{ (i+1) }}</td>
                            <td>{{ emp.name }}</td>
                            <td>{{ emp.email }}</td>
                            <td>{{ emp.phone }}</td>
                            <td>{{ emp.depar}}</td>
                            <td>
                                <button class="btn btn-warning" data-bs-toggle="modal"
                                    data-bs-target="#modal" 
                                    @click="$event => openModal(2,emp.name,emp.email.emp.phone, emp.department.id,emp.id)">
                                    <i class="fa-solid fa-edit"></i>
                                </button>
                            </td>
                            <td>
                                <button class="btn btn-danger"
                                @click="$event => deleteEmployee(emp.id,emp.name)">
                                <i class="fa-solid fa-trash"></i>
                                    </button>
                            </td>
                        </tr>
                    </tbody> 
                </table>
                <Paginate :page-count="row"
                :click-handler="getEmployees"
                :prev-text="'Prev'" :next-text="'Next'"
                :container-class="'pagination'">
                </Paginate>
            </div>
        </div>   
    </div>
    <modal id="'modal'" :title="title">
        <div class="modal-body">
            <form @submit.prevent="save">
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-user"></i>
                    </span>
                    <input autofocus type="text" v-model="form.name"
                    placeholder="Name" class="form-control" 
                    required ref="nameInput">
                </div>
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-at"></i>
                    </span>
                    <input required type="email" v-model="form.email"
                    placeholder="Email" class="form-control">
                </div>
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-phone"></i>
                    </span>
                    <input required type="text" v-model="form.phone"
                    placeholder="Phone" class="form-control">
                </div>
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-building"></i>
                    </span>
                    <SelectInput v-model="form.department_id" :options="departments"
                    class="form-select" required ></SelectInput>
                </div>
                <div class="d-grid col-10 mx-auto">
                            <button class="btn btn-success">
                                <i class="fa-solid fa-save"></i>Save</button>
                        </div>
            </form>
        </div>
        <div class="modal-footer">
            <button class="btn btn-dark" ref="close"
            data-bs-dismiss="modal">close</button>
        </div>
    </modal>
</template>
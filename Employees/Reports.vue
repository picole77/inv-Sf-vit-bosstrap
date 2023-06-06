<script setup>
import {ref,onMounted} from 'vue';
import { useAuthStore } from  '../../stores/auth';
import SelectInput from '../../components/SelectInput.vue';
import DataTable  from 'datatables.net-vue3';
import 'datatables.net-dt/css/jquery.dataTables.css';
import { ButtonHtml5 } from 'datatables.net-buttons/js/buttons.html5';
import 'datatables.net-buttons/js/buttons.print';
import 'datatables.net-responsive-dt';
import 'datatables.net-responsive-dt/css/responsive.dataTables.min.css';
import JsZip from 'jsZip';
import pdfmake from "pdfmake/build/pdfmake";
import * as pdfFonts from "pdfmake/build/vfs_fonts";
pdfmake.vfs = pdfFonts.pdfMake ? pdfFonts.pdfMake.vfs : pdfMake.vfs;
window.jszip = jszip;
DataTable.use(ButtonHtml5);
const authStore = useAuthStore();
axios.defaults.headers.common['Authorization']='Bearer'+authStore.authToken;

const departments = ref([]);
const employees = ref([]);
const columns1 = ref([]);
const colomns2 = ref([]);
const buttons1 = ref ([]);
const button2 = ref([]);
const report = ref ('1');
const types = ref([{'id':'1','name':'Employees'},{'id':'2','name':'Departments'}]);

onMounted(async()=>{
    const d = await axios.get('/api/departaments');
    departments.value = d.data;
    const e = await axios.get('/api/employeesall');
    employees.value = e.data;
});
columns1.value=[{data:null,render:function(data,type,row,meta)
{return (meta.row +1)}},
{data:'name'},
{data:'email'},
{data:'phone'},
{data:'department'},
]
columns2.value=[{data:null,render:function(data,type,row,meta)
{return (meta.row+1)}},
{data:'name'}
]
buttons1.value = [
    {
        title:'Employees',extend:'excelhtml5',
        text:'<i class="fa-solid fa-file-excel"></i> Excel',
        className:'btn btn-success'
    },
    {
        title:'Employees',extend:'pdfhtml5',
        text:'<i class="fa-solid fa-file-pdf"></i> PDF',
        className:'btn btn-danger'
    },
    {
        title:'Employees',extend:'print',
        text:'<i class="fa-solid fa-print"></i> PRINT',
        className:'btn btn-dark'
    },
    {
        title:'Employees',extend:'copy',
        text:'<i class="fa-solid fa-copy"></i> COPY',
        className:'btn btn-secondary'
    },
]
buttons2.value = [
    {
        title:'Department',extend:'excelhtml5',
        text:'<i class="fa-solid fa-file-excel"></i> Excel',
        className:'btn btn-success'
    },
    {
        title:'Department',extend:'pdfhtml5',
        text:'<i class="fa-solid fa-file-pdf"></i> PDF',
        className:'btn btn-danger'
    },
    {
        title:'Department',extend:'print',
        text:'<i class="fa-solid fa-print"></i> PRINT',
        className:'btn btn-dark'
    },
    {
        title:'Department',extend:'copy',
        text:'<i class="fa-solid fa-copy"></i> COPY',
        className:'btn btn-secondary'
    },
]
</script>

<template>
    <div class="row mb-5">
        <div class="col-md-8 offset-md-2">
            Report:
            <select-input id="rep" class="mt-1" v-model="report" :options="types"></select-input>    
         </div>
    </div>
        <div class="row" v-if="report == '1'">
            <div class="col-md-8 offset-md-2">
                <div class="table-responsive">
                    <DataTable :data="employees" :columns="columns1" class="table table-striped" :options="{responsive:true, autowidth:false, dom:'bfrtip',buttons:buttons1}">
                        <thead><tr><th>#</th><th>Nombre</th><th>Email</th><th>Phone</th>Departamento</tr></thead>
                    </DataTable>
                </div>
            </div>
        </div>
        <div class="row" v-else>
            <div class="col-md-8 offset-md-2">
                <div class="table-responsive">
                    <DataTable :data="departments" :columns="columns2" class="table table-striped" :options="{responsive:true, autowidth:false, dom:'bfrtip',buttons:buttons2}">
                        <thead><tr><th>#</th><th>Nombre</th></tr></thead>
                    </DataTable>
                </div>
            </div>
        </div>
</template>
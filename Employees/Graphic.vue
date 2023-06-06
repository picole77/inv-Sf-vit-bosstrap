<script setup>
import {ref,onMounted} from 'vue';
import { useAuthStore } from '../../stores/auth';
import {Bar,Pie} from 'vue-chartjs'
import { Chart as ChartJS, Title, Tooltip, legend, BarElement, CategoryScale,
LinearScale, ArcElement} from 'chart.js';
ChartJS.register(Title, Tooltip, BarElement, CategoryScale,LinearScale,ArcElement);
const authStore = useAuthStore();
axios.defaults.headers.common['Authorization']= 'Bearer'+authStore.authToken;

const departments = ref([]);
const employees = ref([]);
const charData = ref([]);
const chartOptions = ref ([]);
const colors = ref ([]);
const loaded = ref ([false]);
const random = () =>{
    return Math.floor(Math.random() * 256);
}
onMounted(async () =>{
    const info =await axios.get('/api/employeesbydepartment');
    info.data.map((row)=>(
        departments.value.push(row.name),
        employees.value.push(row.cont),
        colors.value.push("rgb("+random()+","+random()+","+random()+")")
        ));
        chartOptions.value={responsive:true}
        CharacterData.value = {
            labels:departments.value,
            datasets:[
                {labe:'Employees', data:employees.value, backgroundColor:colors}
            ]
        };
        loaded.value = true;
});

</script>
<template>
    <div class="row">
        <div class="col-md-8 offset-md-2">
            <bar v-if="loaded" :data="chartData" :options="chartOptions"></bar>
        </div>
    </div>
</template>
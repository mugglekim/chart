<template>
  <div class="student-analysis">
    <h2>학생별 출결 현황</h2>
    <select v-model="selectedStudent">
      <option v-for="list in students" :key="list.id" :value="list">{{ list.name }}</option>
    </select>
    <div class="chart-container">
      <canvas ref="chartCanvas"></canvas>
    </div>
  </div>
</template>

<script setup>
import { Chart, registerables } from 'chart.js';
import { ref, watch } from 'vue';

  defineProps({
    students:Array
  });

  const selectedStudent=ref('');
  const chartCanvas=ref(null);
  let chartInst=null;
  Chart.register(...registerables);

  watch(selectedStudent,()=>{
    // console.log(selectedStudent.value.attendance);
    if(chartInst){
      chartInst.destroy();
    }
    const attendanceCount={present:0, leave:0, absent:0, late:0};
    selectedStudent.value.attendance.forEach((list)=>{
        attendanceCount[list.status]++;
    });

    chartInst=new Chart(chartCanvas.value,{
      type: 'doughnut',
      data: {
        labels: ['출석','지각','결석','조퇴'],
        datasets: [{
          label: '출결현황',
          data: [attendanceCount.present, attendanceCount.late, attendanceCount.absent, attendanceCount.leave],
          backgroundColor:['seagreen','hotpink','tomato','khaki']
        }]
      },
      options: {
        responsive: true,
        plugins: {
          legend: {position:'bottom'} //범례 위치를 하단으로 이동
        }
      }
    });

  });
</script>

<style lang="scss" scoped>
  .student-analysis{
    select{
      width: 70%;
      font-size: 1rem;
      padding: 0.5rem 1rem;
      outline: none;
      border-radius: 4px;
    }
    .chart-container{
      width: 100%; max-width: 300px;
      margin: auto;
    }
  }
</style>
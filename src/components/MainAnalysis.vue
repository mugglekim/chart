<template>
  <div class="main-analysis">
    <h2>전체 출결</h2>
    <div class="chart-container">
      <canvas ref="chartCanvas"></canvas>
    </div>
  </div>
</template>

<script setup>
import { Chart, registerables } from 'chart.js';
import { nextTick, ref, watch } from 'vue';

  const props = defineProps({
    students:Array
  });

  const chartCanvas=ref(null);
  const chartInst=null;
  Chart.register(...registerables);
  //전체 출력 데이터
  const attendanceCount={present:0, leave:0, absent:0, late:0};
  //학생 전체 출결 데이터 카운트
  // console.log(props.students);
  
  watch(()=>props.students, async()=>{
    //학생 전체 출결 데이터 카운트
    props.students.forEach((list)=>{
      list.attendance.forEach((item)=>{
        attendanceCount[item.status]++;
      });
    });
    console.log(attendanceCount);
    await nextTick();
    //차트그리기
    chartInst=new Chart(chartCanvas.value,{
      type: 'line',
      data:{
        labels: ['출석', '지각', '결석', '조퇴'],
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
  .main-analysis{
    .chart-container{
      width: 100%; max-width: 400px;
      margin: auto;
    }
  }
</style>
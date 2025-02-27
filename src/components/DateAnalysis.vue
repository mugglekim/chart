<template>
  <div class="date-analysis">
    <h2>일자별 출결 현황</h2>
    <input type="date" v-model="selectedDate">
    
    <div class="chart-container" v-if="isDate">
      <canvas ref="chartCanvas"></canvas>
    </div>

    <!-- 날짜 선택 전 -->
    <p v-if="!selectedDate">날짜를 선택하세요</p>
    <!-- 날짜 선택 후 데이터 없을 때 -->
    <p v-else-if="!isDate">해당 날짜의 출결 데이터가 없습니다</p>
  </div>
</template>

<script setup>
import { Chart, registerables } from 'chart.js';
import { nextTick, ref, watch } from 'vue';

  const props=defineProps({
    students:Array
  });
  //선택한 날짜
  const selectedDate=ref('');
  const isDate=ref(null);
  //차트 그리기
  let chartInst=null;
  const chartCanvas=ref('');
  Chart.register(...registerables);
  //선택한 날짜가 변경되면
  watch(selectedDate,async()=>{
    // console.log(selectedDate.value); //2025-02-11
    const attendanceCount={present:0, leave:0, absent:0, late:0};
    isDate.value=false;
    props.students.forEach((list)=>{
      // console.log(list);
      list.attendance.forEach((item)=>{
        if(item.date===selectedDate.value){
          attendanceCount[item.status]++;
          isDate.value=true;
        }
      })
    })
    if(chartInst){
      chartInst.destroy();
    }
    //데이터가 없다면 차트를 생성하지 않기
    if(!isDate.value) return;

    //DOM 업데이트가 되면 재갱신
    await nextTick();

    //차트그리기
    chartInst=new Chart(chartCanvas.value,{
      type: 'bar',
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
    .date-analysis{
      input{
        width: 70%;
        font-size: 1rem;
        padding: 0.5rem 1rem;
        outline: none;
        border-radius: 4px;      
      }
      .chart-container{
          width: 100%; max-width: 400px;
          margin: auto;
      }
      p{
        margin-top: 2rem;
      }
    }
</style>
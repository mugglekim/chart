<template>
  <div>
    <canvas ref="chartCanvas"></canvas>
  </div>
</template>

<script setup>
import { Chart, registerables } from 'chart.js';
import { onMounted, ref } from 'vue';

  const chartCanvas=ref(null);
  Chart.register(...registerables);

  onMounted(()=>{
    new Chart(chartCanvas.value,{
      type: 'bar',
      data: {
        labels: ['출석','지각','결석','조퇴'],
        datasets: [
          {
            label: '타이틀',
            data: [10,5,10,2],
            backgroundColor: [
              'rgba(106, 90, 225, 0.6)',
              'rgba(189, 39, 19, 0.6)',
              'rgba(46, 139, 87, 0.6)',
              'rgba(84, 184, 206, 0.6)'
            ],
            borderColor:[
              'rgb(0,0,0)',
              'rgb(0,0,0)',
              'rgb(0,0,0)',
              'rgb(0,0,0)'
            ],
            borderWidth: 1
          }
        ]
      },
      options: {
        responsive: true,
        plugins:{
          title: {
            display: true,
            text: "Chart.js View"
          }
        }
      }
    });
  });
</script>

<style lang="scss" scoped>

</style>
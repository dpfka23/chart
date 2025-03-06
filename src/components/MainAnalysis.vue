<template>
  <div class="main-analysis">
    <h2>전체 출결 사항</h2>
    <div class="chart-container">
      <canvas ref="chartCanvas"></canvas>
    </div>
  </div>
</template>

<script setup>
  import { Chart, registerables } from 'chart.js';
  import { ref, watch, nextTick } from 'vue';

  const props = defineProps({
    students: Array
  });

  const chartCanvas = ref(null);
  let chartInst = null;
  Chart.register(...registerables);
  //전체 출력 데이터
  const attendanceCount = {present:0, leave:0, absent:0, late:0}; 
  watch(()=>props.students, async()=>{
    //학생 전체 출결 데이터 카운트
    props.students.forEach((list)=>{
      list.attendance.forEach((item)=>{
        attendanceCount[item.status]++;
      });
    });
    console.log( attendanceCount );
    await nextTick();
    chartInst = new Chart(chartCanvas.value, {
      type: 'line',
      data: {
          labels: ['출석','지각','결석','조퇴'],
          datasets: [{
            label: '출결현황',
            data: [attendanceCount.present, attendanceCount.late, attendanceCount.absent, attendanceCount.leave],
            backgroundColor: ['cornflowerblue','orange','coral','yellowgreen']
          }]
      },
      options: {
        responsive:true,
        plugins: {
          legend: { position: 'bottom' }
        }
      }
    });
});
</script>

<style lang="scss" scoped>
  .chart-container{
    width: 100%;
    max-width: 400px;
    margin: auto;
  }
</style>
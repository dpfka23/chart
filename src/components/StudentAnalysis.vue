<template>
  <div class="student-analysis">
    <h2>학생별 출결 사항</h2>
    <select v-model="selectedStudent">
      <option v-for="list in students" :key="list.id" :value="list">
        {{ list.name }}
      </option>
    </select>

      <!-- <p>이름: {{ selectedStudent.name }}</p> -->
      <div class="chart-container">
        <canvas ref="chartCanvas"></canvas>
      </div>
      
  </div>
</template>

<script setup>
import { Chart, registerables } from 'chart.js';
import { ref, watch } from 'vue';

  defineProps({
    students : Array
  });
  
  const selectedStudent = ref('');
  const chartCanvas = ref(null);
  let chartInst = null;
  Chart.register(...registerables);

  watch(selectedStudent, ()=>{
    // console.log(selectedStudent.value.attendance);
    if(chartInst) {
      chartInst.destroy();
    }
    const attendanceCount = {present:0, leave:0, absent:0, late:0};
    selectedStudent.value.attendance.forEach((list)=>{
      attendanceCount[list.status]++;
    });

    chartInst = new Chart(chartCanvas.value,{
      type: "doughnut",
      data:{
        labels: ['출석','지각','결석','조퇴'],
        datasets: [{
          label: '출결현황',
          data: [attendanceCount.present, attendanceCount.late, attendanceCount.absent, attendanceCount.leave],
          backgroundColor: ['cornflowerblue','orange','coral','yellowgreen']
        }]
      },
      options: {
        responsive: true,
        plugins: {
          legend: { position: 'bottom' } //범례를 하단으로 배치
        }
      }
    });

});
</script>

<style lang="scss" scoped>
  .chart-container{
    width: 100%;
    max-width: 300px;
    margin: auto;
  }
  select{
    width: 70%;
    height: 2rem;
    font-size: 1rem;
    margin-bottom: 10px;
  }
</style>
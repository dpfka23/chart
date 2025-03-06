<template>
  <div class="date-analysis">
    <h2>일자별 출결 현황</h2>
    <input type="date" v-model="selectedDate"/>
    <div class="chart-container" v-if="isDate">
      <canvas ref="chartCanvas"></canvas>
    </div>
  <!-- 데이터가 없다면 -->
  <p v-else>해당 날짜의 출결 데이터가 없습니다.</p>
  </div>  
</template>

<script setup>
  import { Chart, registerables } from 'chart.js';
  import { ref, watch, nextTick } from 'vue';

  const props = defineProps({
    students: Array
  });
  //선택한 날짜
  const selectedDate = ref('');
  const isDate = ref(null);
  //차트 그리기
  let chartInst = null;
  const chartCanvas = ref('');
  Chart.register(...registerables);
  //선택한 날짜가 변경되면
  watch(selectedDate, async()=>{
    const attendanceCount = {present:0, leave:0, absent:0, late:0};
    isDate.value = false;
    props.students.forEach((list)=>{
      list.attendance.forEach((item)=>{
        if(item.date === selectedDate.value){
          attendanceCount[item.status]++;
          isDate.value = true;
        }
      });
    });
    if(chartInst){
      chartInst.destroy();
    };
    //데이터가 없다면 차트를 생성할 필요가 없음
    if(!isDate.value) return; //isDate값이 false이면 return 한다
    //DOM 업데이트가 되면 재갱신
    await nextTick();
    //차트 그리기 : bar
    chartInst = new Chart(chartCanvas.value, {
      type: 'bar',
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
</style>
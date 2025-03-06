<template>
  <div class="app">
    <h1>출결 관리 대시보드</h1>
    <div class="wrap">
      <MainAnalysis :students="students"/>
      <StudentAnalysis :students="students"/>
      <DateAnalysis :students="students"/>
    </div>
    <!-- <DrawChart /> -->
  </div>
</template>

<script setup>
  import { ref, onMounted } from 'vue';
  import StudentAnalysis from './components/StudentAnalysis.vue';
  import DrawChart from './components/DrawChart.vue';
  import DateAnalysis from './components/DateAnalysis.vue';
  import MainAnalysis from './components/MainAnalysis.vue';
  const students = ref([]);
  const fetchData = async ()=>{
    const response = await fetch('./attendanceData.json');
    const data = await response.json();
    // console.log(data.students);
    students.value = data.students;
  }
  onMounted(fetchData);
</script>

<style lang="scss" scoped>

</style>
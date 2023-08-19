<script setup>
import { ref } from 'vue'
import TopMenu from './components/TopMenu.vue'
import TaskListTable from './components/TaskListTable.vue'
var taskData = ref({})

const updateTaskData = (item) => {
    taskData.value = item;
}

</script>

<template>
  
    <!-- <div> -->
   <h1 class="text-shadow-effect">TASK LIST DEMO</h1>
  <!-- </div>
<div> -->
  <div class="layout-style">
  <TopMenu @task-data="updateTaskData"/>
  <!-- <div>
</div> -->
  <TaskListTable :task-data="taskData"/>
  <!-- </div> -->
</div>
</template>
<!-- 
<style>
body {
  font-family: "Lexend Deca","Helvetica Neue",Helvetica,Arial,sans-serif;
}
</style> -->

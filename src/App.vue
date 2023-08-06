<script setup>
import { ref } from 'vue';
const task = ref("");
const inputRef = ref(null);
const tasks = ref(JSON.parse(localStorage.getItem('tasks')) || [])
const isEditing = ref(false);
const currentTask = ref('');

function addTask(){
  let taskFromLocalStorage = JSON.parse(localStorage.getItem('tasks'));
  if(!taskFromLocalStorage?.length) {
    tasks.value = [];
    if(task.value && task.value !== ' ') {
      tasks.value.push({id: 1, task: task.value});
      localStorage.setItem('tasks', JSON.stringify(tasks.value))
    } else {
      return;
    }
  } else {
    if(task.value && task.value !== ' ') {
      taskFromLocalStorage?.length && tasks.value.push({id: ++taskFromLocalStorage.length, task: task.value})
      localStorage.setItem('tasks', JSON.stringify(tasks.value))
    } else {
      return;
    }
  }
  task.value = ''
}

function editTask(e, id, indx){
  const _currentTask = tasks.value[indx]
  if(_currentTask.id === id) {
    _currentTask.task = e.target.value;
  }
  localStorage.setItem('tasks', JSON.stringify(tasks.value))
  isEditing.value = false;
}
function deleteTask(indx) {
  tasks.value.splice(indx, 1);
  localStorage.setItem('tasks', JSON.stringify(tasks.value))
}

function handleDblClick(e, id) {
  let currentTaskId = Number(e.target.id?.split('task')[1]);

  if(currentTaskId === id) {
    isEditing.value = true;
    currentTask.value = currentTaskId;
  } else {
    currentTask.value = 0;
  }
}
</script>

<template>
  <div class="h-screen w-screen flex justify-center items-center">
    <div>
      <form @submit.prevent="addTask">
      <input class="p-3 focus:border border border-l-6 rounded-l-lg border-l-black focus:outline-0 outline-0" placeholder="Enter task" type="text" v-model="task">
      <button class="bg-black text-white p-3 rounded-r-lg">Add Task</button>
    </form>
    <ul v-if="tasks.length" class="mt-8 bg-whtie shadow-inner max-h-[300px] overflow-auto scrollba">
    <li class="pl-8" v-for="(_task, indx) in tasks" :key="_task.id" @dblclick="(e) => handleDblClick(e,  _task.id, _task.task)">
      <div>
        <div class="flex gap-4 items-center">
          <div class="text-xl" :id='`task${_task.id}`' title="double tap to edit">{{ _task.task }}</div>
          <span class="text-4xl cursor-crosshair text-red-500 mb-[5px]" @click="() => deleteTask(indx)">&times;</span>
        </div>
        <div v-if="isEditing && currentTask === _task.id">
          <input type="text"  @blur="(e)=>editTask(e, _task.id, indx)" :value="_task.task" ref="inputRef">
        </div>
      </div>
    </li>
  </ul>
  <h1 v-else class="mt-11 text-center text-3xl text-gray-500 font-bold">
    Nooo Data
  </h1>
    </div>
  </div>
</template>
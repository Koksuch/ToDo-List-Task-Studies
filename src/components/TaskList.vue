<script setup>
const props = defineProps({
  taskList: Array,
  removeTask: Function,
  editTask: Function
})

const saveOnChanges = () => {
  localStorage.setItem('taskList', JSON.stringify(props.taskList))
}
</script>

<template>
  <div class="tasks" v-for="item in props.taskList" :key="item.id">
    <p v-if="item.done" class="done">{{ item.name }}</p>
    <p v-if="!item.done">{{ item.name }}</p>
    <div class="actions">
      <input class="checkbox" type="checkbox" v-model="item.done" @change="saveOnChanges" />
      <button class="edit-btn" @click="props.editTask(item.id)">Edytuj</button>
      <button class="delete-btn" @click="props.removeTask(item.id)">Usuń</button>
    </div>
    <p class="description">{{ item.taskDescription }}</p>
    <div class="dates">
      <p>Data rozpoczęcia: {{ item.taskStartDate }}</p>
      <p>Data zakończenia: {{ item.taskEndDate }}</p>
    </div>
  </div>
</template>

<style scoped>
.tasks {
  font-size: 20px;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  margin-top: 15px;
  padding: 10px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
}
.done {
  text-decoration: line-through;
  color: #22c55e;
}
.actions {
  display: flex;
  gap: 10px;
}

.checkbox {
  width: 32px;
}

.checkbox:focus {
  outline-color: #22c55e;
}

.delete-btn {
  font-size: 20px;
  border-radius: 4px;
  border: 1px solid transparent;
  background-color: #ef4444;
  color: white;
}
.delete-btn:hover {
  cursor: pointer;
  background-color: #f87171;
}
.delete-btn:focus {
  outline: none;
  border: 1px solid #9f1239;
}

.edit-btn {
  font-size: 20px;
  border-radius: 4px;
  border: 1px solid transparent;
  background-color: #f97316;
  color: white;
}
.edit-btn:hover {
  cursor: pointer;
  opacity: 0.8;
}
.edit-btn:focus {
  outline: none;
  border: 1px solid #b45309;
}

.description {
  width: 100%;
  text-align: left;
  margin-top: 10px;
  font-size: 15px;
  color: #6b7280;
  border-top: #6b7280 1px dotted;
  border-bottom: #6b7280 1px dotted;
}

.dates {
  width: 100%;
  text-align: left;
  margin-top: 10px;
  font-size: 15px;
  color: #6b7280;
  border-top: #6b7280 1px dotted;
  border-bottom: #6b7280 1px dotted;
}
</style>

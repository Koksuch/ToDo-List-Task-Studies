<script setup>
import { onMounted, ref, watch } from 'vue'
import VueDatePicker from '@vuepic/vue-datepicker'
import '@vuepic/vue-datepicker/dist/main.css'
import TaskList from '../components/TaskList.vue'

const taskList = ref(JSON.parse(localStorage.getItem('taskList')) || [])

const taskId = ref(0)
const taskName = ref('')
const taskDescription = ref('')
const taskStartDate = ref('')
const taskEndDate = ref('')
const isEdit = ref(false)
const sortOption = ref('default')
const errorMsg = ref('')

const highestId = () => {
  let max = 0
  taskList.value.filter((item) => {
    if (item.id > max) {
      max = item.id
    }
  })
  return max
}

const addTodo = () => {
  if (!taskName.value || !taskDescription.value || !taskStartDate.value || !taskEndDate.value) {
    errorMsg.value = 'Wypełnij wszystkie pola!'
    return
  }
  if (formatDate(taskStartDate.value) > formatDate(taskEndDate.value)) {
    errorMsg.value = 'Data rozpoczęcia nie może być późniejsza niż data zakończenia!'
    return
  }
  errorMsg.value = ''

  taskList.value.push({
    id: highestId() + 1,
    name: taskName.value.trim(),
    taskDescription: taskDescription.value.trim(),
    taskStartDate: taskStartDate.value,
    taskEndDate: taskEndDate.value,
    done: false
  })
  taskName.value = ''
  taskDescription.value = ''
  taskStartDate.value = ''
  taskEndDate.value = ''

  localStorage.setItem('taskList', JSON.stringify(taskList.value))
  console.log(taskList.value)
}

const removeTask = (id) => {
  taskList.value.filter((item, index) => {
    if (item.id === id) {
      taskList.value.splice(index, 1)
      localStorage.setItem('taskList', JSON.stringify(taskList.value))
    }
  })
}

const editTask = (id) => {
  taskList.value.filter((item) => {
    if (item.id === id) {
      taskId.value = item.id
      taskName.value = item.name
      taskDescription.value = item.taskDescription
      taskStartDate.value = item.taskStartDate
      taskEndDate.value = item.taskEndDate
      isEdit.value = true
    }
  })
}

const saveEditTask = () => {
  if (!taskName.value || !taskDescription.value || !taskStartDate.value || !taskEndDate.value) {
    errorMsg.value = 'Wypełnij wszystkie pola!'
    return
  }
  if (formatDate(taskStartDate.value) > formatDate(taskEndDate.value)) {
    errorMsg.value = 'Data rozpoczęcia nie może być późniejsza niż data zakończenia!'
    return
  }
  errorMsg.value = ''

  taskList.value.filter((item) => {
    if (item.id === taskId.value) {
      item.name = taskName.value
      item.taskDescription = taskDescription.value
      item.taskStartDate = taskStartDate.value
      item.taskEndDate = taskEndDate.value
    }
  })
  taskId.value = 0
  taskName.value = ''
  taskDescription.value = ''
  taskStartDate.value = ''
  taskEndDate.value = ''
  isEdit.value = false

  localStorage.setItem('taskList', JSON.stringify(taskList.value))
}

const formatDate = (date) => {
  const formatedDate = new Date(
    date.replace(/(\d{2})\.(\d{2})\.(\d{4}) (\d{2}:\d{2})/, '$3-$2-$1T$4')
  )
  return formatedDate
}

onMounted(() => {
  sortOption.value = 'default'
  taskList.value.sort((a, b) => a.id - b.id)
  localStorage.setItem('taskList', JSON.stringify(taskList.value))
})

watch(
  () => sortOption.value,
  () => {
    if (sortOption.value === 'default') {
      taskList.value.sort((a, b) => a.id - b.id)
    } else if (sortOption.value === 'name') {
      taskList.value.sort((a, b) => a.name.localeCompare(b.name))
    } else if (sortOption.value === 'taskStartDate') {
      taskList.value.sort((a, b) => {
        const dateA = formatDate(a.taskStartDate)
        const dateB = formatDate(b.taskStartDate)
        return dateA - dateB
      })
    } else if (sortOption.value === 'taskEndDate') {
      taskList.value.sort((a, b) => {
        const dateA = formatDate(a.taskEndDate)
        const dateB = formatDate(b.taskEndDate)
        return dateA - dateB
      })
    } else if (sortOption.value === 'done') {
      taskList.value.sort((a, b) => a.done - b.done)
    }
    localStorage.setItem('taskList', JSON.stringify(taskList.value))
  }
)

watch(
  () => taskName.value,
  (newV) => {
    console.log(taskName.value)
    console.log(newV)
  }
)
</script>
<template>
  <h1>Lista zadań</h1>
  <div class="add-task">
    <input class="task-name-input" type="text" placeholder="Tytuł zadania" v-model="taskName" />
    <textarea
      class="task-description-input"
      type="text"
      placeholder="Opis zadania"
      v-model="taskDescription"
    />

    <p>Data rozpoczęcia:</p>
    <VueDatePicker
      placeholder="Wybierz datę i czas"
      v-model="taskStartDate"
      model-type="dd.MM.yyyy HH:mm"
      format="dd/MM/yyyy HH:mm"
      :min-date="new Date()"
    />
    <p>Data zakończenia</p>
    <VueDatePicker
      placeholder="Wybierz datę i czas"
      v-model="taskEndDate"
      model-type="dd.MM.yyyy HH:mm"
      format="dd/MM/yyyy HH:mm"
      :min-date="new Date()"
    />
    <p class="error-msg" v-if="errorMsg">{{ errorMsg }}</p>
    <input v-if="!isEdit" class="add-task-btn" type="button" value="Dodaj" @click="addTodo" />
    <input v-if="isEdit" class="edit-task-btn" type="button" value="Edytuj" @click="saveEditTask" />
  </div>
  <div class="sortOptions">
    <label for="sort">Sortuj po:</label>
    <select name="sort" id="sort" v-model="sortOption">
      <option value="default">Domyślnie</option>
      <option value="name">Nazwie(a-z)</option>
      <option value="taskStartDate">Dacie rozpoczęcia(od najwcześniejszej)</option>
      <option value="taskEndDate">Dacie zakończenia(od najwcześniejszej)</option>
      <option value="done">Statusie(zakończone na końcu)</option>
    </select>
  </div>
  <TaskList :taskList="taskList" :removeTask="removeTask" :editTask="editTask" />
</template>
<style scoped>
h1 {
  font-size: 40px;
  margin-bottom: 10px;
}
.add-task {
  width: 100%;
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  justify-content: center;
  margin-bottom: 20px;
}
.add-task-btn {
  width: 100%;
  font-size: 30px;
  border-radius: 4px;
  margin-top: 15px;
  padding: 5px;
  border: 1px solid transparent;
  background-color: #3b82f6;
  color: white;
}
.add-task-btn:hover {
  cursor: pointer;
  opacity: 0.8;
}
.add-task-btn:focus {
  outline: none;
  border: 1px solid #4338ca;
}

.edit-task-btn {
  width: 100%;
  font-size: 30px;
  border-radius: 4px;
  margin-top: 15px;
  padding: 5px;
  border: 1px solid transparent;
  background-color: #f97316;
  color: white;
}
.edit-task-btn:hover {
  cursor: pointer;
  opacity: 0.8;
}
.edit-task-btn:focus {
  outline: none;
  border: 1px solid #b45309;
}

textarea {
  min-height: 100px;
}
.add-task input[type='text'],
textarea,
select {
  font-size: 20px;
  border-radius: 4px;
  width: 100%;
  border: 1px solid #ddd;
  padding: 5px;
}
.add-task input[type='text']:focus,
textarea:focus,
select:focus {
  outline: none;
  border: 1px solid #aaaeb7;
}

select:hover {
  cursor: pointer;
}
.sortOptions {
  font-size: 20px;
  margin-bottom: 20px;
}
.add-task p {
  font-size: 20px;
}

.error-msg {
  color: #ef4444;
  font-weight: bold;
}
</style>

<script setup>
import ToDoListView from './views/ToDoListView.vue'
</script>

<template>
  <div class="container">
    <ToDoListView />
  </div>
</template>

<style scoped>
.container {
  background-color: white;
  padding: 30px;
  border-radius: 20px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  text-align: center;
  margin: 20px auto;
  width: 50%;
}
@media screen and (max-width: 1000px) {
  .container {
    width: 70%;
  }
}

@media screen and (max-width: 740px) {
  .container {
    width: 90%;
  }
}

@media screen and (max-width: 600px) {
  .container {
    width: 100%;
  }
}
</style>

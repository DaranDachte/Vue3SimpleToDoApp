<script setup lang="ts">
import { v4 as uuidv4 } from "uuid";
import { ref, onMounted, watch } from "vue";

type ToDo = {
  text: string;
  isDone: boolean;
  id: string;
};

const todos = ref<ToDo[]>([]);
const input = ref("");

const addTodo = () => {
  if (input.value.trim() === "") return;
  const todo = { text: input.value.trim(), isDone: false, id: uuidv4() };
  todos.value.unshift(todo);
  input.value = "";
};

const deleteToDo = (id: string) => {
  todos.value = todos.value.filter((todo) => todo.id !== id);
};
watch(
  todos,
  (newTodoValue) => {
    localStorage.setItem("todos", JSON.stringify(newTodoValue));
  },
  { deep: true }
);

onMounted(() => {
  todos.value = localStorage.getItem("todos")
    ? JSON.parse(localStorage.getItem("todos")!)
    : [];
});
</script>

<template>
  <main
    class="flex relative w-full min-h-screen flex-col justify-center items-center bg-white"
  >
    <section class="greeting">
      <h3 class="title">ToDo Application</h3>
    </section>
    <div class="input-section">
      <section class="create-todo">
        <form @submit.prevent="addTodo">
          <h3>What do you plan on doing?</h3>
          <input type="text" placeholder="add your todo" v-model="input" />
          <input type="submit" value="Add todo" />
        </form>
      </section>
    </div>
    <div class="todo-section">
      <section class="todo-list">
        <h2 v-show="todos.length === 0">No Todos Here</h2>
        <div class="list">
          <div
            v-for="todo in todos"
            :class="`todo-item ${todo.isDone && 'done'}`"
          >
            <label>
              <input type="checkbox" v-model="todo.isDone" />
            </label>
            <div class="todo-content">
              <input type="text" v-model="todo.text" />
            </div>
            <div class="actions">
              <button class="delete" @click="deleteToDo(todo.id)">
                Delete
              </button>
            </div>
          </div>
        </div>
      </section>
    </div>
  </main>
</template>

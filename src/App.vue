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
    class="flex relative w-full min-h-screen flex-col justify-center items-center bg-[#c8e5e7]"
  >
    <div class="bg-[]">
      <h3 class="text-[3rem] font-black text-[#00563B]">ToDo Application</h3>

      <form @submit.prevent="addTodo">
        <h3 class="text-[#00563B] font-semibold text-[1.2rem]">
          With using Vue 3 framework and Tailwind
        </h3>
        <div class="flex justify-between items-center">
          <div class="">
            <input
              type="text"
              class="bg-[#e7ffde] outline-0 rounded h-[2rem]"
              placeholder="add your todo"
              v-model="input"
            />
          </div>
          <div
            class="text-[#00563B] bg-[#e7ffde] font-semibold ml-[0.3rem] p-[0.3rem] rounded"
          >
            <input
              class="cursor-pointer h-[1.5rem]"
              type="submit"
              value="Add todo"
            />
          </div>
        </div>
      </form>
    </div>
    <div class="">
      <h2 v-show="todos.length === 0">No Todos Here</h2>
      <div class="">
        <div
          v-for="todo in todos"
          :class="`todo-item ${todo.isDone && 'done'}`"
        >
          <div
            class="flex items-center justify-between bg-[#e7ffde] h-[3rem] my-[1rem] rounded px-[1rem]"
          >
            <div class="pr-[0.3rem]">
              <label>
                <input type="checkbox" v-model="todo.isDone" />
              </label>
            </div>

            <div class="text-[#00563B]">
              <p>{{ todo.text }}</p>
            </div>

            <div class="ml-[0.3rem] p-[0.3rem] rounded bg-[#E34234]">
              <button
                class="text-[#00563B] font-semibold"
                @click="deleteToDo(todo.id)"
              >
                Delete
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

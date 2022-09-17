<template>
  <header class="absolute inset-x-0 top-0">
    <div class="container flex justify-end p-4 mx-auto">
      <button
          class="overflow-hidden p-2"
          @click="switchTheme()"
      >
        <transition
            enter-active-class="transition duration-200 ease-out"
            leave-active-class="transition duration-200 ease-in"
            :enter-from-class="currentTheme === 'dark' ? 'transform -translate-y-full scale-50 opacity-0' : 'transform translate-y-full scale-50 opacity-0'"
            enter-to-class="transform translate-y-0"
            leave-from-class="transform translate-y-0"
            :leave-to-class="currentTheme === 'dark' ? 'transform translate-y-full scale-50 opacity-0' : 'transform -translate-y-full scale-50 opacity-0'"
            mode="out-in"
        >
          <svg
              v-if="currentTheme === 'dark'"
              xmlns="http://www.w3.org/2000/svg"
              class="w-8 h-8"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
          >
            <path d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z" />
          </svg>

          <svg
              v-else
              xmlns="http://www.w3.org/2000/svg"
              class="w-8 h-8"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
          >
            <path d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
          </svg>
        </transition>
      </button>
    </div>
  </header>

  <main class="app">
    <section class="greeting">
      <h2 class="title dark:text-gray-100">Welcome, <input type = "text" placeholder="Name here"
       v-model="name"/>
       </h2>
    </section>

    <section class="create-todo">
      <form @submit.prevent="addTodo()">
        <h4 class="">ADD A TASK</h4>
        <input class="" type="text" placeholder="e.g make a toast"
        v-model="input_content" />
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input type="radio"
                   name="categoy"
                   value="business"
                   v-model="input_category"
            />

            <span class="bubble business"></span>
            <div>Not urgent</div>
          </label>
          <label>
            <input type="radio"
                   name="category"
                   value="personal"
                   v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Urgent</div>
          </label>
         <!-- {{ input_category }} -->
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`" :key="todo.createdAt">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
<!--            <div class="">{{todo.content }}</div>-->
             <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
           <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script setup>
import { ref, onMounted, computed, watch} from 'vue';
import { currentTheme, initTheme, switchTheme } from '@/composables/theme.js';

const todos = ref([])
const name = ref(null)
const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return a.createdAt - b.createdAt
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null)
    return
  console.log("addTodo")
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
     createdAt: new Date().getTime()
  })
  input_content.value = '';
  input_category.value = null;
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal));
}, { deep: true})

watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
})

onMounted(() => {
  initTheme();
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
})
</script>

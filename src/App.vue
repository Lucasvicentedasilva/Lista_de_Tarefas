<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createAt - a.createAt
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

 

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}), { deep: true }

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">

    <section class="greeting">
      <h2 class="title">
        E aí, <input type="text" v-model="name" placeholder="Insira seu nome aqui" />
      </h2>

    </section>

    <section class="create-todo">
      <h3 class="">
        CRIE UMA TAREFA
      </h3>

      <form @submit.prevent="addTodo">
        <h4>O que está na sua lista de tarefas?</h4>
        <input type="text"
         v-model="input_content" 
         placeholder="Nome da tarefa" />


        <h4>Escolha uma categoria</h4>

        <div class="options">
          <label>
            <input type="radio" 
            name="category"
            value="business" 
            v-model="input_category"/>

            <span class="bubble business"></span>

            <div>Trabalho</div>
          </label>

          <label>
            <input type="radio" 
            value="personal" 
            v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Pessoal</div>
          </label>

        </div>

        <input type="submit" value="Add tarefa" />
      </form>
    </section>



    <section class="todo-list">
      <h3 class="list">

        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">

          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Apagar</button>
          </div>

        </div>

      </h3>
    </section>
  </main>
</template>



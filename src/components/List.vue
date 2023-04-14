<script setup>
import { computed, onMounted, ref, watch } from 'vue';

const todos = ref ([])
const name = ref ('')

const input_content = ref ('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) => {
  return a.createdAt - b.createdAt
}))

const addTodo  = () =>  {
    if (input_content.value.trim() === '' || input_category.value === null) {
      return 
    }

    todos.value.push({
      content: input_content.value,
      category: input_category.value,
      name: name.value,
      done: false,
      createdAt: new Date().getTime()
    })

    input_content.value = ''
    input_category.value = null
}

const removeTodo = (todo)=> {
    todos.value = todos.value.filter((current) => current !== todo)
}

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))

}, {deep: true})

watch(name, (newVal) =>  {
  localStorage.setItem('name', newVal)
})

onMounted(()=>{
  name.value  = localStorage.getItem('name') ||  ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>
    <main class="app">
      <section class="saudacao">
        <h2 class="title">Opa! E aí, <input type="text" placeholder="seu nome aqui..." v-model="name" /></h2>
      </section>

      <section class="create-todo">
        <form @submit.prevent="addTodo">
          <h4>O que deseja adcionar na sua lista de tarefas?</h4>
          <input type="text" placeholder="Adcionar item aqui..." v-model="input_content">
          
          <h4>Selecione uma categoria</h4>

          <div class="options">
            <label>
              <input type="radio" name="category" id="category1" value="business" v-model="input_category">
              <span class="bubble business"></span>
              <div>de Casa</div>
            </label>

            <label>
              <input type="radio" name="category" id="category2" value="personal" v-model="input_category">
              <span class="bubble personal"></span>
              <div>do Trabalho</div>
            </label>
            
          </div>

          <input type="submit" value="Adcionar item a lista" :disabled="input_content.length <=2" />

        </form>
      </section>
      
      <section class="todo-list">
        <h3><b>Suas Listas de Tarefas</b> - <i><small>Total: {{ todos.length }}</small></i></h3>
        <small>(Dê um check para marcar como finalizada)</small>

        <div v-if="todos.length === 0" class="empty">
          <img src="../assets/img/empty-01.svg" alt="" title="">
          <h2>Tudo vazio por aqui!</h2>
        </div>

          <div class="list">
            <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'} todo-${todo.category}`">
              <label>
                <input type="checkbox" v-model="todo.done" />
                <span :class="`bubble ${todo.category}`"></span>
              </label>

              <div class="todo-content">
                <input type="text" v-model="todo.content" /> <small v-if="todo.name">Item adcionado por {{ todo.name }}</small>
              </div>
            
              <div class="actions">
                <button class="delete" @click="removeTodo(todo)"><span class="material-symbols-outlined">delete</span></button>
              </div>

            </div>
          </div>
      </section>

    </main>

</template>
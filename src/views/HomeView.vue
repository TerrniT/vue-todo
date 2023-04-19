<script setup lang="ts">
import { ref, onMounted, computed, watch } from 'vue'


interface Todo {
  content: string
  category: string
  status: boolean
  createdAt: number 
}

const todos = ref<Todo[]>([])
const name = ref<string>('')

const inputContent = ref<string>('')
const inputCategory = ref<string | null>(null)

const todoAscSort = computed(() => todos.value.sort((a,b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if(inputContent.value.trim() === '' || inputCategory.value === null) {
    return 
  }

  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    status: false,
    createdAt: new Date().getTime()
  })

  inputContent.value = ''
  inputCategory.value = null
}

const removeTodo = (todo: Todo) => {
  todos.value = todos.value.filter(item => item !== todo)
}

// dont work without deep == true, cuz todos needs to be a completly new value 
// not a mutated array
watch(todos, newValue => {
  localStorage.setItem('todos', JSON.stringify(newValue))
}, {deep: true})

watch(name, (newValue) => {
  localStorage.setItem(
    'name',
    newValue
  )
})

onMounted(()=> {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || [] 
})

</script>

<template>
  <main class="">
    <section>
      <h2>What's up, <input type="text" placeholder="Name here" v-model="name"/></h2>
    </section>

    <section>
      <h3>Create a todo</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input type="text" placeholder="Learn Vue3" v-model="inputContent"/>
        <h4>Pick a category</h4>
        <div>
          <label>
            <input type="radio" name="category"  value="business" v-model="inputCategory"/>
            <span>Business</span>
          </label>

          <label>
            <input type="radio" name="category"  value="personal" v-model="inputCategory"/>
            <span>Personal</span>
          </label>
        </div>

        <input type="submit" value="Add Todo" class="border border-zinc-400 hover:cursor-pointer" />
      </form>
    </section>

    <section>
      <h3>Todo List</h3>
      <div>
        <div v-for="todo in todoAscSort" :class="`${todo.status ? 'bg-zinc-600' : ''}`">
          <label><input type="checkbox" v-model="todo.status"/>
            <div :class="` rounded-full w-5 h-5 ${todo.category == 'business' ? 'bg-purple-500' : ''}`"></div>
          </label>
          <div><input type="text" v-model="todo.content"/></div>
          <div>
            <button class="text-xs bg-red-500/20 text-red-500 " @click="removeTodo(todo)">Delete</button>
          </div>

        </div>
      </div>

    </section>

  </main>
</template>

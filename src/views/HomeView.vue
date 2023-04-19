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
  <main class="max-w-md mx-auto">
    <section class="mt-4 bg-gray-50 p-6 rounded-md">
      <h2>What's up, <input type="text" placeholder="Name here" v-model="name" class="bg-transparent text-pink-700 font-medium outline-none placeholder:font-normal"/></h2>
    </section>

    <section class="mt-4">
      <h3 class="text-pink-800 font-bold">## Create a todo</h3>
      <form @submit.prevent="addTodo" class="mt-4">
        <h4 class="text-pink-600 italic">### What's on your todo list?</h4>
        <input type="text" placeholder="Type something" v-model="inputContent" class="w-full bg-transparent mt-2 border border-gray-300 px-3 py-2 rounded-md"/>

        <div class="mt-4">
        <h4>Pick a category</h4>
        <div class="mt-2 flex gap-3 w-full">
          <label class="flex flex-col gap-2 bg-gray-50 py-4 px-8 items-center rounded-md border border-gray-200 w-full">
            <input type="radio" name="category"  value="work" v-model="inputCategory"/>
            <span>Work</span>
          </label>

          <label class="flex flex-col gap-2 bg-gray-50 py-4 px-8 items-center rounded-md border border-gray-200 w-full">
            <input type="radio" name="category"  value="personal" v-model="inputCategory" />
            <span>Personal</span>
          </label>

        </div>
        </div>

        <input type="submit" value="Add Todo" class="border border-zinc-400 hover:cursor-pointer px-3 py-2 rounded-md mt-4 w-full bg-green-600 text-white hover:bg-green-500 duration-100 transition-all" />
      </form>
    </section>

    <section class="mt-4">
      <h3 class="text-pink-800 font-bold">## Todo List</h3>
      <div class="w-full flex gap-1 flex-col">
        <div v-for="todo in todoAscSort" :class="`${todo.status ? 'text-zinc-300' : ''} w-full flex p-1 items-center justify-between`">
          <div class="flex w-full pr-2">
          <label class="flex items-center gap-3">
            <input type="checkbox" v-model="todo.status"/>
            <div :class="` rounded-full w-3 h-3 ${todo.category == 'business' ? 'bg-purple-500' : 'bg-blue-500'}`"></div>
          </label>
            <input type="text" v-model="todo.content" class="bg-transparent border-b hover:border-zinc-400 outline-none p-1 duration-100 transition-all border-white w-full"/>
            </div>
          <div>
            <button class=" px-2 py-1 rounded-md hover:text-white hover:bg-red-500/80 duration-150 transition-all text-xs bg-red-500/20 text-red-500 " @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>

  </main>
</template>

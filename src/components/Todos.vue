<script setup lang="ts">
import { ref , reactive , computed} from 'vue'
import TODOComponent from './TODOComponent.vue'
const title = ref("Todos")

const selected = ref("tout")

var todos = reactive([
  {
    intitule: 'Todo 1',
    etat: 'Faite',
    date: '2016-04-10',
  },
  {
    intitule: 'Todo 2',
    etat: 'Faite',
    date: '2019-05-20',
  },
  {
    intitule: 'Todo 3',
    etat: 'Faite',
    date: '2001-12-04',
  },
  {
    intitule: 'Todo 4',
    etat: 'En attente',
    date: '2024-12-04',
  },
])

interface Todo {
  intitule: string
  etat: string
  date: string
}


function handleAddTodo(newTodo:Todo) {
  todos.push(newTodo) // Update the todos array
}

function handleDeleteTodo(index:number) {
  todos.splice(index,1)
}

const remainingTasks = computed(() => {
  return todos.filter(todo => todo.etat === 'A faire').length
})

const selectedTodos = computed(() => {
  if(selected.value === 'tout' || selected.value === 'clear' || selected.value === 'delete' ){
    return todos
  }else if(selected.value === 'a faire'){
    return todos.filter((e) => e.etat === 'A faire')
  }else{
    return todos.filter((e) => e.etat === 'Faite')
  }
})

function clear(event:Event){
  todos = []
  selected.value = 'clear'
}

function deleteTerminatedTodos(event:Event){
  event.preventDefault()
  todos = todos.filter((e) => e.etat !== 'Faite')
  selected.value = 'delete'
}

// Mise à jour de l'intitulé d'une tâche
function handleUpdateTodo(index: number, updatedIntitule: string) {
  todos[index].intitule = updatedIntitule
}

</script>

<template>
  <div class="todos">
    <h1>{{ title }}</h1>
    <div>Sélectionné : {{ selected }}</div>
    <select v-model="selected">
      <option disabled value="">Sélectionne qu'une seule </option>
      <option value="tout">toutes les tâches</option>
      <option value="a faire">les tâches à faire</option>
      <option value="faite">les tâches faites</option>
    </select>
    <button v-on:click="clear">
        Tout supprimer
    </button>
    <button v-on:click="deleteTerminatedTodos">
       Supprimer les taches terminées
    </button>
    <TODOComponent :todos="selectedTodos" @add-todo="handleAddTodo" @delete-todo="handleDeleteTodo" @update-todo="handleUpdateTodo"/>
    <footer v-if="todos.length > 0">
      <span>Il reste {{ remainingTasks }} tâche{{ remainingTasks > 1 ? 's' : '' }} à faire.</span>
  </footer>
  </div>
</template>
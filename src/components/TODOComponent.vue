<script setup lang="ts">
import { reactive , ref } from 'vue'

const name = ref('')

interface Todo {
  intitule: string
  etat: string
  date: string
}

// Déclarez `todos` comme un tableau d'objets de type `Todo`
defineProps<{
  todos: Todo[]
}>()


// Declare the custom event
const emit = defineEmits(['add-todo','delete-todo','update-todo'])

function addToTodoList(event: Event) {
  event.preventDefault()
  if (name.value.trim()) {
    const newTodo = {
      intitule: name.value.trim(),
      etat: 'A faire',
      date: new Date().toISOString().split('T')[0],
    }
    emit('add-todo', newTodo) // Emit the new todo to the parent
    name.value = '' // Reset input field
  }
}

function deleteTodo(index:number) {
  emit('delete-todo', index)
}

const editingIndex = ref<number | null>(null)

// Fonction pour démarrer l'édition
function startEditing(index: number) {
  editingIndex.value = index
}

// Fonction pour enregistrer les modifications
function saveEdit(index: number, event: Event) {
  const input = event.target as HTMLInputElement
  const updatedIntitule = input.value.trim()
  if (updatedIntitule) {
    emit('update-todo', index, updatedIntitule)
  }
  editingIndex.value = null // Quitter le mode édition
}

// Fonction pour annuler l'édition
function cancelEdit() {
  editingIndex.value = null
}

</script>

<template>
  <div class="todocomponent">
    <form @submit="addToTodoList">
      <label for="add" class="hidden-visually">Nom de la todo : </label>
      <input type="text" name="name" id="add" v-model="name" />
      <button type="submit">
        <i class="fas fa-search" aria-hidden="true"></i>
        <span class="hidden-visually">Ajouter</span>
      </button>
    </form>
    <ul>
      <li v-for="(todo, index) in todos" :key="todo.date">
        <!-- Mode édition -->
        <div v-if="editingIndex === index">
          <input
            type="text"
            :value="todo.intitule"
            @blur="saveEdit(index, $event)"
            @keyup.enter="saveEdit(index, $event)"
            @keyup.esc="cancelEdit"
          />
        </div>

        <!-- Mode affichage -->
          <div v-else @dblclick="startEditing(index)">
            {{ todo.intitule }} - {{ todo.etat }} - {{ todo.date }}
            <button @click="deleteTodo(index)" aria-label="Supprimer cette tâche">
              🗑️
            </button>
          </div>
      </li>
    </ul>
  </div>
</template>
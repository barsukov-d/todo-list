<script setup>
import { ref, watch } from 'vue'

const STORAGE_KEY = 'tudulist-tasks'

function loadTasks() {
  try {
    const raw = localStorage.getItem(STORAGE_KEY)
    return raw ? JSON.parse(raw) : []
  } catch {
    return []
  }
}

const tasks = ref(loadTasks())
const newTaskText = ref('')

watch(
  tasks,
  (value) => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(value))
  },
  { deep: true }
)

function addTask() {
  const text = newTaskText.value.trim()
  if (!text) return
  tasks.value.push({ id: Date.now(), text, done: false })
  newTaskText.value = ''
}

function toggleTask(id) {
  const task = tasks.value.find((t) => t.id === id)
  if (task) task.done = !task.done
}

function removeTask(id) {
  tasks.value = tasks.value.filter((t) => t.id !== id)
}
</script>

<template>
  <div class="app">
    <h1 class="title">Tudulist</h1>

    <form class="add-form" @submit.prevent="addTask">
      <input
        v-model="newTaskText"
        class="add-input"
        type="text"
        placeholder="Новая задача..."
      />
      <button type="submit" class="add-button">Добавить</button>
    </form>

    <ul class="task-list">
      <li v-for="task in tasks" :key="task.id" class="task-item" :class="{ done: task.done }">
        <label class="task-label">
          <input
            type="checkbox"
            class="task-checkbox"
            :checked="task.done"
            @change="toggleTask(task.id)"
          />
          <span class="task-text">{{ task.text }}</span>
        </label>
        <button type="button" class="remove-button" @click="removeTask(task.id)">✕</button>
      </li>
    </ul>

    <p v-if="tasks.length === 0" class="empty-state">Список задач пуст</p>
  </div>
</template>

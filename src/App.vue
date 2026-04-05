<template>
  <div :class="['app-shell', { dark: darkMode }]">
    <aside class="sidebar">
      <div class="sidebar-top">
        <p class="sidebar-eyebrow">Workspace</p>
        <h2>ProjectFlow</h2>
        <p class="sidebar-text">
          Manage projects, track progress, and keep tasks moving.
        </p>
      </div>

      <div class="sidebar-section">
        <button class="btn btn-primary full-btn" @click="showProjectModal = true">
          + New Project
        </button>
        <button class="btn btn-secondary full-btn" @click="openAddTaskModal">
          + New Task
        </button>
      </div>

      <div class="sidebar-section">
        <p class="sidebar-label">Projects</p>

        <div v-if="projects.length" class="project-list">
          <button
            v-for="project in projects"
            :key="project.id"
            :class="['project-item', { active: selectedProjectId === project.id }]"
            @click="selectedProjectId = project.id"
          >
            <div>
              <strong>{{ project.name }}</strong>
              <span>{{ project.tasks.length }} tasks</span>
            </div>
          </button>
        </div>

        <div v-else class="sidebar-empty">
          No projects yet
        </div>
      </div>

      <div v-if="selectedProject" class="sidebar-section project-summary-card">
        <p class="sidebar-label">Selected Project</p>
        <h3>{{ selectedProject.name }}</h3>

        <div class="mini-stats">
          <div>
            <span>Total</span>
            <strong>{{ selectedProject.tasks.length }}</strong>
          </div>
          <div>
            <span>Done</span>
            <strong>{{ doneCount }}</strong>
          </div>
          <div>
            <span>Open</span>
            <strong>{{ openCount }}</strong>
          </div>
        </div>

        <button class="btn btn-danger-outline full-btn" @click="openDeleteProjectModal">
          Delete Project
        </button>
      </div>

      <div v-if="selectedProject" class="sidebar-section activity-card">
        <p class="sidebar-label">Activity Log</p>

        <div v-if="selectedProject.activityLog?.length" class="activity-list">
          <div
            v-for="item in displayedActivityLog"
            :key="item.id"
            class="activity-item"
          >
            <strong>{{ item.action }}</strong>
            <span>{{ item.timestamp }}</span>
          </div>
        </div>

        <div v-else class="sidebar-empty">
          No activity yet
        </div>
      </div>
    </aside>

    <div class="main-panel">
      <AppHeader
        :darkMode="darkMode"
        :taskCount="selectedProject?.tasks?.length || 0"
        @toggle-dark="toggleDarkMode"
        @open-project-modal="showProjectModal = true"
        @open-task-modal="openAddTaskModal"
      />

      <main class="container">
        <section class="top-controls">
          <div class="top-grid">
            <ProjectSelector
              :projects="projects"
              :selectedProjectId="selectedProjectId"
              @select-project="selectedProjectId = $event"
            />

            <div class="filters-row three-cols">
              <SearchBar v-model="searchQuery" />
              <PriorityFilter v-model="selectedPriority" />
              <SortSelect v-model="selectedSort" />
            </div>
          </div>

          <section v-if="selectedProject" class="stats-grid">
            <div class="stat-card">
              <span class="stat-label">Total Tasks</span>
              <strong class="stat-value">{{ selectedProject.tasks.length }}</strong>
            </div>

            <div class="stat-card">
              <span class="stat-label">Completed</span>
              <strong class="stat-value">{{ doneCount }}</strong>
            </div>

            <div class="stat-card">
              <span class="stat-label">In Progress</span>
              <strong class="stat-value">{{ progressCount }}</strong>
            </div>

            <div class="stat-card">
              <span class="stat-label">Progress</span>
              <strong class="stat-value">{{ completionRate }}%</strong>
              <div class="progress-bar">
                <div class="progress-fill" :style="{ width: `${completionRate}%` }"></div>
              </div>
            </div>
          </section>
        </section>

        <ProjectBoard
          :tasks="filteredTasks"
          @edit-task="openEditTaskModal"
          @delete-task="openDeleteTaskModal"
          @change-status="changeTaskStatus"
          @task-dropped="handleTaskDrop"
        />
      </main>
    </div>

    <ToastContainer :toasts="toasts" @remove="removeToast" />

    <AddTaskModal
      v-if="showAddTaskModal"
      @close="showAddTaskModal = false"
      @save="addTask"
    />

    <EditTaskModal
      v-if="showEditTaskModal"
      :task="editingTask"
      @close="closeEditModal"
      @save="saveEditedTask"
    />

    <div v-if="showProjectModal" class="modal-overlay" @click.self="showProjectModal = false">
      <div class="modal-card small">
        <div class="modal-header">
          <h2>Create Project</h2>
          <button class="icon-btn" @click="showProjectModal = false">✕</button>
        </div>

        <div class="form-group">
          <label>Project Name</label>
          <input v-model="newProjectName" type="text" placeholder="e.g. Website Redesign" />
        </div>

        <div class="modal-actions">
          <button class="btn btn-secondary" @click="showProjectModal = false">Cancel</button>
          <button class="btn btn-primary" @click="createProject">Create</button>
        </div>
      </div>
    </div>

    <div v-if="showDeleteTaskModal" class="modal-overlay" @click.self="closeDeleteTaskModal">
      <div class="modal-card small">
        <div class="modal-header">
          <h2>Delete Task</h2>
          <button class="icon-btn" @click="closeDeleteTaskModal">✕</button>
        </div>

        <p class="confirm-text">
          Are you sure you want to delete
          <strong>{{ taskToDelete?.title }}</strong>?
        </p>

        <div class="modal-actions">
          <button class="btn btn-secondary" @click="closeDeleteTaskModal">Cancel</button>
          <button class="btn btn-danger" @click="confirmDeleteTask">Delete</button>
        </div>
      </div>
    </div>

    <div v-if="showDeleteProjectModal" class="modal-overlay" @click.self="closeDeleteProjectModal">
      <div class="modal-card small">
        <div class="modal-header">
          <h2>Delete Project</h2>
          <button class="icon-btn" @click="closeDeleteProjectModal">✕</button>
        </div>

        <p class="confirm-text">
          Are you sure you want to delete
          <strong>{{ selectedProject?.name }}</strong> and all its tasks?
        </p>

        <div class="modal-actions">
          <button class="btn btn-secondary" @click="closeDeleteProjectModal">Cancel</button>
          <button class="btn btn-danger" @click="confirmDeleteProject">Delete Project</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref, watch } from 'vue'
import { useLocalStorage } from './composables/useLocalStorage'

import AppHeader from './components/AppHeader.vue'
import ProjectBoard from './components/ProjectBoard.vue'
import AddTaskModal from './components/AddTaskModal.vue'
import EditTaskModal from './components/EditTaskModal.vue'
import SearchBar from './components/SearchBar.vue'
import PriorityFilter from './components/PriorityFilter.vue'
import ProjectSelector from './components/ProjectSelector.vue'
import SortSelect from './components/SortSelect.vue'
import ToastContainer from './components/ToastContainer.vue'

function formatTimestamp(date) {
  return new Intl.DateTimeFormat('en-GB', {
    dateStyle: 'short',
    timeStyle: 'short'
  }).format(date)
}

const baseTime = Date.now()

const defaultProjects = [
  {
    id: baseTime,
    name: 'Website Redesign',
    tasks: [
      {
        id: baseTime + 1,
        createdAt: baseTime + 1,
        title: 'Design landing page',
        description: 'Create hero section and CTA',
        priority: 'High',
        dueDate: '2026-04-10',
        status: 'todo',
        labels: ['Design', 'Frontend']
      },
      {
        id: baseTime + 2,
        createdAt: baseTime + 2,
        title: 'Setup contact form',
        description: 'Build and validate contact form',
        priority: 'Medium',
        dueDate: '2026-04-12',
        status: 'progress',
        labels: ['Backend', 'Forms']
      },
      {
        id: baseTime + 3,
        createdAt: baseTime + 3,
        title: 'Publish final content',
        description: 'Upload final copy and images',
        priority: 'Low',
        dueDate: '2026-04-01',
        status: 'done',
        labels: ['Content']
      }
    ],
    activityLog: [
      {
        id: baseTime + 100,
        action: 'Project created',
        timestamp: formatTimestamp(new Date())
      }
    ]
  }
]

const projects = useLocalStorage('pm_projects', defaultProjects)
const darkMode = useLocalStorage('pm_dark_mode', false)

const selectedProjectId = ref(projects.value[0]?.id || null)
const searchQuery = ref('')
const selectedPriority = ref('All')
const selectedSort = ref('newest')

const showAddTaskModal = ref(false)
const showEditTaskModal = ref(false)
const showProjectModal = ref(false)

const showDeleteTaskModal = ref(false)
const showDeleteProjectModal = ref(false)

const editingTask = ref(null)
const newProjectName = ref('')
const taskToDelete = ref(null)

const toasts = ref([])

const selectedProject = computed(() => {
  return projects.value.find((project) => project.id === selectedProjectId.value) || null
})

const filteredTasks = computed(() => {
  if (!selectedProject.value) return []

  let tasks = selectedProject.value.tasks.filter((task) => {
    const q = searchQuery.value.toLowerCase()

    const matchesSearch =
      task.title.toLowerCase().includes(q) ||
      task.description.toLowerCase().includes(q) ||
      (task.labels || []).some((label) => label.toLowerCase().includes(q))

    const matchesPriority =
      selectedPriority.value === 'All' || task.priority === selectedPriority.value

    return matchesSearch && matchesPriority
  })

  tasks = [...tasks].sort((a, b) => {
    if (selectedSort.value === 'newest') {
      return (b.createdAt || b.id) - (a.createdAt || a.id)
    }

    if (selectedSort.value === 'oldest') {
      return (a.createdAt || a.id) - (b.createdAt || b.id)
    }

    if (selectedSort.value === 'due-soon') {
      if (!a.dueDate && !b.dueDate) return 0
      if (!a.dueDate) return 1
      if (!b.dueDate) return -1
      return new Date(a.dueDate) - new Date(b.dueDate)
    }

    if (selectedSort.value === 'due-late') {
      if (!a.dueDate && !b.dueDate) return 0
      if (!a.dueDate) return 1
      if (!b.dueDate) return -1
      return new Date(b.dueDate) - new Date(a.dueDate)
    }

    return 0
  })

  return tasks
})

const doneCount = computed(() => {
  if (!selectedProject.value) return 0
  return selectedProject.value.tasks.filter((task) => task.status === 'done').length
})

const progressCount = computed(() => {
  if (!selectedProject.value) return 0
  return selectedProject.value.tasks.filter((task) => task.status === 'progress').length
})

const openCount = computed(() => {
  if (!selectedProject.value) return 0
  return selectedProject.value.tasks.filter((task) => task.status !== 'done').length
})

const completionRate = computed(() => {
  if (!selectedProject.value || selectedProject.value.tasks.length === 0) return 0
  return Math.round((doneCount.value / selectedProject.value.tasks.length) * 100)
})

const displayedActivityLog = computed(() => {
  return (selectedProject.value?.activityLog || []).slice(0, 8)
})

watch(
  projects,
  (newProjects) => {
    if (!newProjects.find((p) => p.id === selectedProjectId.value)) {
      selectedProjectId.value = newProjects[0]?.id || null
    }
  },
  { deep: true }
)

function toggleDarkMode() {
  darkMode.value = !darkMode.value
  pushToast(`Switched to ${darkMode.value ? 'dark' : 'light'} mode`)
}

function createProject() {
  const name = newProjectName.value.trim()
  if (!name) return

  const newProject = {
    id: Date.now(),
    name,
    tasks: [],
    activityLog: [
      {
        id: Date.now() + 500,
        action: 'Project created',
        timestamp: formatTimestamp(new Date())
      }
    ]
  }

  projects.value.unshift(newProject)
  selectedProjectId.value = newProject.id
  newProjectName.value = ''
  showProjectModal.value = false

  pushToast('Project created successfully')
}

function openAddTaskModal() {
  if (!selectedProject.value) return
  showAddTaskModal.value = true
}

function addTask(taskData) {
  if (!selectedProject.value) return

  selectedProject.value.tasks.unshift({
    id: Date.now(),
    createdAt: Date.now(),
    labels: taskData.labels || [],
    ...taskData
  })

  addActivity(`Task "${taskData.title}" created`)
  pushToast('Task added successfully')
  showAddTaskModal.value = false
}

function openEditTaskModal(task) {
  editingTask.value = {
    ...task,
    labels: [...(task.labels || [])]
  }
  showEditTaskModal.value = true
}

function closeEditModal() {
  editingTask.value = null
  showEditTaskModal.value = false
}

function saveEditedTask(updatedTask) {
  if (!selectedProject.value) return

  const taskIndex = selectedProject.value.tasks.findIndex((task) => task.id === updatedTask.id)
  if (taskIndex !== -1) {
    selectedProject.value.tasks[taskIndex] = {
      ...selectedProject.value.tasks[taskIndex],
      ...updatedTask,
      labels: updatedTask.labels || []
    }
  }

  addActivity(`Task "${updatedTask.title}" updated`)
  pushToast('Task updated successfully')
  closeEditModal()
}

function openDeleteTaskModal(task) {
  taskToDelete.value = task
  showDeleteTaskModal.value = true
}

function closeDeleteTaskModal() {
  taskToDelete.value = null
  showDeleteTaskModal.value = false
}

function confirmDeleteTask() {
  if (!selectedProject.value || !taskToDelete.value) return

  const deletedTitle = taskToDelete.value.title

  selectedProject.value.tasks = selectedProject.value.tasks.filter(
    (task) => task.id !== taskToDelete.value.id
  )

  addActivity(`Task "${deletedTitle}" deleted`)
  pushToast('Task deleted')
  closeDeleteTaskModal()
}

function openDeleteProjectModal() {
  if (!selectedProject.value) return
  showDeleteProjectModal.value = true
}

function closeDeleteProjectModal() {
  showDeleteProjectModal.value = false
}

function confirmDeleteProject() {
  if (!selectedProject.value) return

  const deletedName = selectedProject.value.name
  projects.value = projects.value.filter((project) => project.id !== selectedProject.value.id)
  selectedProjectId.value = projects.value[0]?.id || null
  closeDeleteProjectModal()

  pushToast(`Project "${deletedName}" deleted`)
}

function changeTaskStatus({ taskId, status }) {
  if (!selectedProject.value) return

  const task = selectedProject.value.tasks.find((task) => task.id === taskId)
  if (task && task.status !== status) {
    task.status = status
    addActivity(`Task "${task.title}" moved to ${statusLabel(status)}`)
    pushToast(`Task moved to ${statusLabel(status)}`)
  }
}

function handleTaskDrop({ taskId, newStatus }) {
  if (!selectedProject.value) return

  const task = selectedProject.value.tasks.find((task) => task.id === taskId)
  if (task && task.status !== newStatus) {
    task.status = newStatus
    addActivity(`Task "${task.title}" dragged to ${statusLabel(newStatus)}`)
    pushToast(`Task moved to ${statusLabel(newStatus)}`)
  }
}

function addActivity(action) {
  if (!selectedProject.value) return

  if (!selectedProject.value.activityLog) {
    selectedProject.value.activityLog = []
  }

  selectedProject.value.activityLog.unshift({
    id: Date.now() + Math.random(),
    action,
    timestamp: formatTimestamp(new Date())
  })
}

function pushToast(message) {
  const id = Date.now() + Math.random()
  toasts.value.push({ id, message })

  setTimeout(() => {
    toasts.value = toasts.value.filter((toast) => toast.id !== id)
  }, 2600)
}

function removeToast(id) {
  toasts.value = toasts.value.filter((toast) => toast.id !== id)
}

function statusLabel(status) {
  if (status === 'todo') return 'To Do'
  if (status === 'progress') return 'In Progress'
  return 'Done'
}
</script>

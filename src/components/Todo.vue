<script setup>
import { ref, onMounted } from 'vue'

const tasks = ref([])
const newTask = ref([])
const totalTasks = ref(0)
const activeFilter = ref(1)

let dataLocalStorage = ref(JSON.parse(localStorage.getItem('todolist')))

function setNewTask() {
    if(newTask.value) {
        tasks.value.push({
            name: newTask.value,
            status: false
        })
        newTask.value = ''
        saveTasksLocalStorage()
    }
}

function completedTask(index) {
    tasks.value[index].status = !tasks.value[index].status
    saveTasksLocalStorage()
}

function deleteTask(index) {
    tasks.value.splice(index, 1)
    saveTasksLocalStorage()
}

function setClassTask(status) {
    return status ? 'done' : ''
}

const getTotalTasks = () => {
    let itemNotCompleted = tasks.value.filter(task => task.status === false).length

    totalTasks.value = itemNotCompleted != 1 ? itemNotCompleted + ' items left' : itemNotCompleted + ' item left'
}

function saveTasksLocalStorage () {
    localStorage.setItem('todolist', JSON.stringify(tasks.value))
    
    getTotalTasks()
}

// Filter
function filtersTasks(filter) {
    tasks.value = dataLocalStorage.value
    if(filter == 'all') {
        activeFilter.value = 1
        tasks.value = dataLocalStorage.value
    }
    if(filter == "active") {
        activeFilter.value = 2
        tasks.value = tasks.value.filter(task => task.status === false)
    }
    if(filter == "completed") {
        activeFilter.value = 3
        tasks.value = tasks.value.filter(task => task.status === true)
    }
    
}


// Clear completed tasks
function clearCompletedTasks() {
    for (var i = tasks.value.length - 1; i >= 0; --i) {
        if (tasks.value[i].status == true) {
            tasks.value.splice(i,1);
        }
    }
    saveTasksLocalStorage()
}

onMounted(() => {
    if(dataLocalStorage.value) {
        tasks.value = dataLocalStorage.value
    } else {
        tasks.value = []
    }
    getTotalTasks()
})

</script>

<template>

    <section id="todo">
        <div class="container">
            <div class="todo-header">
                <h1 class="todo-header__title">Todo</h1>
                <div class="todo-header__switch">
                    <button>DARK</button>
                </div>
            </div>

            <div class="todo-form">
                <form @submit.prevent="setNewTask">
                    <input type="text" placeholder="Press Enter to save task..." v-model="newTask">
                </form>
            </div>

            <div class="todo-content">
                <div class="todo-tasks">
                    <div
                        class="todo-task grid"
                        v-for="(task, index) in tasks"
                        :class="setClassTask(task.status)"
                        v-if="tasks.length > 0"
                    >
                        <span class="todo-task__completed">
                            <button @click="completedTask(index)"></button>
                        </span>
                        <span class="todo-task__name" @click="completedTask(index)">{{ task.name }} - {{ task.status }}</span>
                        <span class="todo-task__delete">
                            <button @click="deleteTask(index)"></button>
                        </span>
                    </div>
                    <div class="todo-task" v-else>There are no elements</div>
                </div>
                <div class="todo-footer">
                    <div class="todo-footer__total">{{ totalTasks }}</div>
                    <div class="todo-footer__filters">
                        <button class="todo-action todo-action--all" :class="{'active' : activeFilter == 1}" @click="filtersTasks('all')">All</button>
                        <button class="todo-action todo-action--active" :class="{'active' : activeFilter == 2}" @click="filtersTasks('active')">Active</button>
                        <button class="todo-action todo-action--completed" :class="{'active' : activeFilter == 3}" @click="filtersTasks('completed')">Completed</button>
                    </div>
                    <div class="todo-footer__clear">
                        <button class="todo-action todo-action--clean-completed" @click="clearCompletedTasks">Clear Completed</button>
                    </div>
                </div>
            </div>
        </div>
    </section>

</template>
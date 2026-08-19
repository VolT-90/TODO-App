<script setup>
import { computed, ref } from "vue";

const savedTasks = JSON.parse(localStorage.getItem("tasks"));

const newTask = ref("");

const editingTaskId = ref(null);

const tasks = ref(savedTasks || []);

const searchedText = ref("");

const generateid = () => {
  return Math.floor(1000 + Math.random() * 9000);
};

//Search About the task
const filteredTasks = computed(() => {
  return tasks.value.filter((task) =>
    task.content.toLowerCase().includes(searchedText.value.toLowerCase()),
  );
});

// Add Task
function addTask() {
  // Handle Update
  if (editingTaskId.value != null) {
    const udpatedTask = tasks.value.find(
      (udpatedTask) => editingTaskId.value == udpatedTask.id,
    );
    if (udpatedTask) {
      udpatedTask.content = newTask.value;
      editingTaskId.value = null;

      newTask.value = "";
      localStorage.setItem("tasks", JSON.stringify(tasks.value));
    }
  } else {
    const task = {
      id: generateid(),
      content: newTask.value,
    };

    if (newTask.value == "") {
      return;
    }

    tasks.value.push(task);

    console.log(task);
    console.log(tasks.value[0]);

    localStorage.setItem("tasks", JSON.stringify(tasks.value));

    newTask.value = "";
  }
}

// Delete Task
function deleteTask(deletedTaskid) {
  const deletedTaskIndex = tasks.value.findIndex(
    (task) => task.id === deletedTaskid,
  );
  tasks.value.splice(deletedTaskIndex, 1);
  localStorage.setItem("tasks", JSON.stringify(tasks.value));
}

//Update Task
function updateTask(taskid) {
  const task = tasks.value.find((task) => taskid == task.id);
  if (task) {
    editingTaskId.value = task.id;
    newTask.value = task.content;
  }
}

//Search about the task
function search(searchedTask) {}
</script>

<template>
  <div class="todo-app container">
    <!-- Header -->
    <h1>Simple TODO App</h1>
    <!-- Search Input -->
    <div class="searchInput container">
      <input
        v-model="searchedText"
        placeholder="Search on your task"
        type="text"
        class="search-input"
      />
    </div>

    <!-- Add Task -->
    <div class="add-task">
      <input v-model="newTask" type="text" placeholder="Add a new task" />

      <button class="add-btn" @click="addTask">
        <svg v-if="!editingTaskId" viewBox="0 0 24 24">
          <path d="M12 5v14M5 12h14" />
        </svg>

        <svg v-else viewBox="0 0 24 24">
          <path d="M4 20h4L19 9l-4-4L4 16v4z" />
          <path d="M13.5 6.5l4 4" />
        </svg>
      </button>
    </div>

    <!-- Tasks -->
    <section class="task-section">
      <div class="task-list-container">
        <div class="task-list">
          <h3>
            <span>{{ tasks.length }}</span>
            Items
          </h3>

          <div class="task-container">
            <div v-for="task in filteredTasks" :key="task.id" class="task">
              <span>{{ task.content }}</span>

              <div class="actions">
                <button @click="deleteTask(task.id)" class="delete-btn">
                  <svg viewBox="0 0 24 24">
                    <path d="M6 7h12M9 7V4h6v3M8 7v13h8V7" />
                  </svg>
                </button>

                <button class="update-btn" @click="updateTask(task.id)">
                  <svg viewBox="0 0 24 24">
                    <path d="M4 20h4L19 9l-4-4L4 16v4z" />
                    <path d="M13.5 6.5l4 4" />
                  </svg>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<style scoped>
* {
  box-sizing: border-box;
}

.container {
  width: 85%;
  margin: auto;
  padding: 0 20px;
}
.todo-app {
  h1 {
    text-align: center;
  }

  min-height: 100vh;
  padding: 51px 20px;

  background: #0d0713;
  color: #fff;
  font-family: Arial, sans-serif;

  .searchInput {
    display: flex;
    justify-content: center;
    width: 50%;
  }
}

/* Add task */

.add-task {
  width: 70%;
  margin-inline: auto;
  margin-top: 100px;
  padding: 0 20px;
  display: flex;
  align-items: center;
  gap: 11px;
}

.search-input {
  margin-bottom: 20px;
  padding-right: 20px;
}

.add-task input {
  flex: 1;
  height: 40px;
  padding: 0 14px;
  border: 1px solid #54208b;
  border-radius: 10px;
  outline: none;
  background: transparent;
  color: #fff;
  font-size: 16px;
}

.search-input {
  flex: 1;
  height: 40px;
  padding: 0 14px;
  border: none;
  border-radius: unset;
  border-bottom: 1px solid #54208b;
  outline: none;
  background: transparent;
  color: #fff;
  font-size: 16px;
}

.add-task input::placeholder {
  color: #9b6dc8;
}

.add-btn {
  width: 40px;
  height: 40px;
  border: 0;
  border-radius: 9px;
  background: #9364c7;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.add-btn svg {
  width: 24px;
  height: 24px;
  fill: none;
  stroke: #fff;
  stroke-width: 1.5;
}

/* Sections */

.task-section {
  margin-top: 58px;
}

.done-section {
  margin-top: 59px;
}

h3 {
  margin: 0 0 16px;
  font-size: 16px;
  font-weight: 400;
  color: #f4eef8;
}

h3 span {
  margin-left: 4px;
}

/* Tasks */

.task-section {
  display: flex;
  flex-direction: column;
}

.task-list-container {
  display: flex;
  align-items: center;
  justify-content: center;
}

.task-list {
  display: flex;
  width: 60%;
  flex-direction: column;
}

.task-container {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.task {
  height: 76px;
  padding: 0 21px;
  border-radius: 11px;
  background: #15101c;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.task > span {
  color: #a276d0;
  font-size: 16px;
}

.actions {
  display: flex;
  align-items: center;
  gap: 16px;
}

.actions button {
  padding: 0;
  border: 0;
  background: transparent;
  cursor: pointer;
}

.delete-btn svg,
.update-btn svg {
  width: 22px;
  height: 22px;
  fill: none;
  stroke: #a574d5;
  stroke-width: 1.5;
}
/* Done */

.done-task {
  height: 75px;
  padding: 0 21px;
  border-radius: 11px;
  background: #15101c;
  display: flex;
  align-items: center;
}

.done-task span {
  color: #55bda5;
  font-size: 16px;
  text-decoration: line-through;
}
</style>

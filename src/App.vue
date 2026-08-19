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
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
}

.todo-app {
  
  padding: 51px 20px;
  color: #fff;
  font-family: Arial, sans-serif;
}

.todo-app h1 {
  text-align: center;
  margin: 0;
  font-size: 32px;
}

/* =========================
   Search
========================= */

.searchInput {
  display: flex;
  justify-content: center;
  width: 50%;
  margin: 35px auto 0;
}

.search-input {
  width: 100%;
  height: 40px;
  padding: 0 14px;
  border: none;
  border-radius: 0;
  border-bottom: 1px solid #54208b;
  outline: none;
  background: transparent;
  color: #fff;
  font-size: 16px;
}

.search-input::placeholder {
  color: #9b6dc8;
}

/* =========================
   Add Task
========================= */

.add-task {
  width: 70%;
  max-width: 850px;
  margin: 70px auto 0;
  padding: 0 20px;
  display: flex;
  align-items: center;
  gap: 11px;
}

.add-task input {
  flex: 1;
  min-width: 0;
  height: 40px;
  padding: 0 14px;
  border: 1px solid #54208b;
  border-radius: 10px;
  outline: none;
  background: transparent;
  color: #fff;
  font-size: 16px;
}

.add-task input::placeholder {
  color: #9b6dc8;
}

.add-btn {
  flex: 0 0 40px;
  width: 40px;
  height: 40px;
  border: 0;
  border-radius: 9px;
  background: #9364c7;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition:
    transform 0.2s ease,
    background 0.2s ease;
}

.add-btn:hover {
  background: #a574d5;
  transform: translateY(-1px);
}

.add-btn svg {
  width: 24px;
  height: 24px;
  fill: none;
  stroke: #fff;
  stroke-width: 1.5;
}

/* =========================
   Sections
========================= */

.task-section {
  margin-top: 58px;
  display: flex;
  flex-direction: column;
}

.task-list-container {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.task-list {
  width: 60%;
  max-width: 850px;
  display: flex;
  flex-direction: column;
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

/* =========================
   Tasks
========================= */

.task-container {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.task {
  min-height: 76px;
  padding: 16px 21px;
  border-radius: 11px;
  background: #15101c;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
}

.task > span {
  min-width: 0;
  color: #a276d0;
  font-size: 16px;
  line-height: 1.5;
  overflow-wrap: anywhere;
}

.actions {
  flex: 0 0 auto;
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
  transition:
    stroke 0.2s ease,
    transform 0.2s ease;
}

.delete-btn:hover svg,
.update-btn:hover svg {
  stroke: #c49ae8;
  transform: scale(1.08);
}

/* =========================
   Done
========================= */

.done-section {
  margin-top: 59px;
}

.done-task {
  min-height: 75px;
  padding: 16px 21px;
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

/* =========================
   Large Tablet
========================= */

@media (max-width: 1024px) {
  .container {
    width: 90%;
  }

  .searchInput {
    width: 65%;
  }

  .add-task {
    width: 80%;
    margin-top: 60px;
  }

  .task-list {
    width: 75%;
  }
}

/* =========================
   Tablet
========================= */

@media (max-width: 768px) {
  .todo-app {
    padding: 40px 15px;
    margin-top: 50px;
  }

  .todo-app h1 {
    font-size: 28px;
  }

  .container {
    width: 100%;
    padding: 0 15px;
  }

  .searchInput {
    width: 80%;
    margin-top: 30px;
  }

  .add-task {
    width: 90%;
    margin-top: 50px;
    padding: 0;
  }

  .task-list {
    width: 90%;
  }

  .task-section {
    margin-top: 45px;
  }

  .task {
    min-height: 70px;
    padding: 14px 17px;
  }
}

/* =========================
   Mobile
========================= */

@media (max-width: 480px) {
  .todo-app {
    padding: 30px 12px;
  }

  .todo-app h1 {
    font-size: 24px;
    line-height: 1.3;
  }

  .container {
    padding: 0 10px;
  }

  /* Search */

  .searchInput {
    width: 100%;
    margin-top: 25px;
  }

  .search-input {
    height: 38px;
    font-size: 14px;
  }

  /* Add Task */

  .add-task {
    width: 100%;
    margin-top: 40px;
    padding: 0;
    gap: 8px;
  }

  .add-task input {
    height: 40px;
    font-size: 14px;
    padding: 0 12px;
  }

  .add-btn {
    flex: 0 0 40px;
    width: 40px;
    height: 40px;
  }

  /* Tasks */

  .task-section {
    margin-top: 40px;
  }

  .task-list-container {
    width: 100%;
  }

  .task-list {
    width: 100%;
  }

  h3 {
    font-size: 14px;
    margin-bottom: 12px;
  }

  .task-container {
    gap: 12px;
  }

  .task {
    min-height: 64px;
    padding: 12px 14px;
    border-radius: 9px;
    gap: 12px;
  }

  .task > span {
    font-size: 14px;
    line-height: 1.4;
  }

  .actions {
    gap: 10px;
  }

  .delete-btn svg,
  .update-btn svg {
    width: 20px;
    height: 20px;
  }
}

/* =========================
   Very Small Phones
========================= */

@media (max-width: 360px) {
  .todo-app {
    padding: 25px 8px;
  }

  .todo-app h1 {
    font-size: 22px;
  }

  .add-task {
    gap: 6px;
  }

  .add-task input {
    font-size: 13px;
    padding: 0 10px;
  }

  .add-btn {
    flex-basis: 36px;
    width: 36px;
    height: 36px;
  }

  .task {
    padding: 10px 12px;
  }

  .task > span {
    font-size: 13px;
  }

  .actions {
    gap: 7px;
  }

  .delete-btn svg,
  .update-btn svg {
    width: 18px;
    height: 18px;
  }
}
</style>

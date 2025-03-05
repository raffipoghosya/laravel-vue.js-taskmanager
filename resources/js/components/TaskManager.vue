<template>
    <div class="container">
      <h1>Task Manager 📝</h1>
  
      <!-- Տեսակավորման ընտրացանկ -->
      <label for="sort" class="sort-label">Տեսակավորել ըստ կարգավիճակի:</label>
      <select v-model="sortBy" class="sort-dropdown">
        <option value="all">Բոլորը</option>
        <option value="pending">To Do</option>
        <option value="in_progress">In Progress</option>
        <option value="completed">Done</option>
      </select>
  
      <!-- Task Ավելացնելու Ֆորմա -->
      <form @submit.prevent="addTask" class="task-form">
        <input v-model="newTask.title" placeholder="Նոր Task" required />
        <textarea v-model="newTask.description" placeholder="Նկարագրություն"></textarea>
        <button type="submit">Ավելացնել</button>
      </form>
  
      <!-- Task-երի Ցուցակ -->
      <ul class="task-list">
        <li v-for="task in filteredTasks" :key="task.id" class="task-card">
          <div class="task-info">
            <h3>{{ task.title }}</h3>
            <p>{{ task.description }}</p>
          </div>
  
          <select v-model="task.status" @change="updateStatus(task)" class="status-dropdown">
            <option value="pending">To Do</option>
            <option value="in_progress">In Progress</option>
            <option value="completed">Done</option>
          </select>
  
          <button @click="deleteTask(task.id)" class="delete-btn">Ջնջել</button>
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  import axios from "axios";
  
  export default {
    data() {
      return {
        tasks: [],
        sortBy: "all",
        newTask: { title: "", description: "", status: "pending" },
      };
    },
    computed: {
      filteredTasks() {
        if (this.sortBy === "all") return this.tasks;
        return this.tasks.filter(task => task.status === this.sortBy);
      },
    },
    methods: {
      async fetchTasks() {
        const response = await axios.get("/api/tasks");
        this.tasks = response.data;
      },
      async addTask() {
        const response = await axios.post("/api/tasks", this.newTask);
        this.tasks.push(response.data);
        this.newTask = { title: "", description: "", status: "pending" };
      },
      async updateStatus(task) {
        await axios.patch(`/api/tasks/${task.id}/status`, { status: task.status });
      },
      async deleteTask(id) {
        await axios.delete(`/api/tasks/${id}`);
        this.tasks = this.tasks.filter(task => task.id !== id);
      },
    },
    mounted() {
      this.fetchTasks();
    },
    
  };
  </script>
  
  <style scoped>
  .container {
    width: 90%;
    max-width: 600px;
    background: white;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
    text-align: center;
  }
  .sort-label {
    font-weight: bold;
    margin-right: 10px;
  }
  .sort-dropdown {
    padding: 5px;
    margin-bottom: 15px;
    border-radius: 5px;
    border: 1px solid #ccc;
  }
  .task-form {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-bottom: 20px;
  }
  .task-form input,
  .task-form textarea,
  .task-form button {
    width: 100%;
    padding: 10px;
    border-radius: 5px;
    border: 1px solid #ccc;
  }
  .task-form button {
    background-color: #007bff;
    color: white;
    border: none;
    cursor: pointer;
  }
  .task-list {
    list-style: none;
    padding: 0;
  }
  .task-card {
    background: white;
    padding: 15px;
    border-radius: 5px;
    margin-bottom: 10px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
  }
  .task-info {
    flex: 1;
    text-align: left;
  }
  .task-info h3 {
    margin: 0;
    font-size: 18px;
    color: #333;
  }
  .status-dropdown {
    padding: 5px;
    border-radius: 5px;
    border: 1px solid #ccc;
    background-color: #f9f9f9;
  }
  .delete-btn {
    background-color: #dc3545;
    color: white;
    border: none;
    padding: 8px 12px;
    border-radius: 5px;
    cursor: pointer;
  }

  </style>
  
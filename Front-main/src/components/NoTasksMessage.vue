<template>
    <div class="container-fluid d-flex align-items-center justify-content-center no-tasks">
      <div class="welcome-text text-center animate-fall">
        <h1 class="aucun">{{ title }}</h1>
        <p class="lead">{{ subtitle }}</p>
      </div>
      <button class="add-btn" @click="onAddTask">
        +
      </button>
    </div>
  </template>
  
  <script>
  export default {
    name: "NoTasksMessage",
    props: {
      title: {
        type: String,
        default: "Aucune tâche ?"
      },
      subtitle: {
        type: String,
        default: "C'est le moment idéal pour en ajouter une ! 🚀"
      }
    },
    methods: {
      onAddTask() {
        this.$emit("add-task");
      }
    }
  };
  </script>
  
  <style scoped>
  .welcome-text h1 {
    font-weight: bold;
    color: #491784;
  }
  .welcome-text .lead {
    color: black;
    font-weight: bold;
  }
  .add-btn {
    border: none;
    font-size: 2rem;
    color: #ffffff;
    background-color: #491784;
    border-radius: 50%;
    padding: 0px 10px;
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    transition: background-color 0.3s ease, transform 0.3s ease;
    margin-left: 20px;
  }
  .add-btn:hover {
    background-color: #7b39b1;
    transform: scale(1.1);
  }
  </style>
  
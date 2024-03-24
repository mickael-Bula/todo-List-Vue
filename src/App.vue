<template>
  <div class="header">
    <h1 class="title">To-do List<img class="logo" src="/vuejs-icon.svg"  alt="logo Vue"/></h1>
    <h3 class="subtitle">avec persistance des données dans le localStorage</h3>
    <to-do-form @todo-added="handleCreate" @todo-edit="handleEdit"></to-do-form>
  </div>        
  <div class="list-container">
      <ul id="myUL" class="tasks">
        <template v-for="item in tasks" :key="item.id">
          <component
            :is="item.isEditing ? 'ToDoItemEdit' : 'ToDoItemDisplay'"
            v-bind="item"
            @todo-delete="handleDelete"
            @todo-check="handleCheck"
            @start-editing="startEditing"
            @todo-edit="handleEdit"
            @edit-cancelled="getIsEdited"
          />
        </template>
      </ul>
  </div>
</template>

<script>
import ToDoForm from "./components/ToDoForm.vue";
import ToDoItemDisplay from './components/ToDoItemDisplay.vue';
import ToDoItemEdit from './components/ToDoItemEdit.vue';

// Il faut choisir entre 'export default' comme ici et la balise <script setup>
//!\\ La fermeture de la balise <script> précédente génère une erreur au build, même commentée !
export default {
  name: "App",
  components: {
    ToDoForm,
    ToDoItemDisplay,
    ToDoItemEdit
  },
  data() {
    return {
      tasks: JSON.parse(localStorage.getItem("tasks")) || [],
    }
  },
  methods: {
    getId() {
      if (this.tasks.length === 0) return 0;
      const ids = this.tasks.map((task) => task.id);
      return Math.max(...ids);
    },
    nextId() {
      return this.getId() + 1;
    },
    handleCreate(label) {
      console.log("dans handleCreate", label);
      const data = { label: label, done: false };
      data.id = this.nextId();
      this.tasks.push(data);
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    getIndexById(id) {
      for (let i = 0; i < this.tasks.length; i++) {
        if (this.tasks[i].id === parseInt(id, 10)) {
          return i;
        }
      }
      return -1;
    },
    handleUpdate(data) {
      let index = this.getIndexById(data.id);
      if (index !== -1) {
        this.tasks[index] = data;
        localStorage.setItem("tasks", JSON.stringify(this.tasks));
      }
    },
    handleDelete(id) {
      console.log("dans handleDelete", id);
      let index = this.getIndexById(id);
      if (index !== -1) {
        this.tasks.splice(index, 1);
        localStorage.setItem("tasks", JSON.stringify(this.tasks));
      }
    },
    handleCheck(id) {
      console.log("dans handleCheck", id);
      // Récupération de la tâche à partir de son id pour alterner la valeur de 'done' à chaque clic, puis mise à jour dans le localStorage
      let index = this.getIndexById(id);
      if (index !== -1) {
        const task = this.tasks[index];
        task.done = !task.done;
        this.handleUpdate(task);
      }
    },
    // cette méthode n'est pas touchée, il faut corriger
    handleEdit(id, newLabel) {
      console.log("dans handleEdit");
      const index = this.getIndexById(id);
      if (index !== -1) {
        const task = this.tasks[index];
        task.label = newLabel;
        task.isEditing = false;
        this.handleUpdate(task);
      }
    },
    startEditing(id) {
      this.tasks.forEach(task => {
        task.isEditing = task.id === id;
      });
    },
    getIsEdited(id, status, label) {
      let index = this.getIndexById(id);
      if (index !== -1) {
        this.tasks[index].isEditing = status;
        this.tasks[index].label = label;
      }
    }
  }
}
</script>

<style scoped>

</style>

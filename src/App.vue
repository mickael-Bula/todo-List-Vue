<template>
  <div class="header">
    <h1 class="title">To-do List<img class="logo" src="/vuejs-icon.svg" /></h1>
    <h3 class="subtitle">avec persistance des données dans le localStorage</h3>
    <to-do-form @todo-added="handleCreate"></to-do-form>
  </div>        
  <div class="list-container">
      <ul id="myUL" class="tasks">
        <li v-for="item in tasks" :key="item.id" class="task">
          <to-do-item
            :label="item.label" 
            :done="item.done"
            :id="item.id"
            @todo-delete="handleDelete">
          </to-do-item>
        </li>
      </ul>
  </div>
</template>

<script>
import ToDoForm from "./components/ToDoForm.vue";
import ToDoItem from "./components/TodoItem.vue";

// Il faut choisir entre 'export default' comme ici et la balise <script setup>
//!\\ La fermeture de la balise <script> précédente génère une erreur au build, même commenté !
export default {
  name: "app",
  components: {
    ToDoForm,
    ToDoItem,
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
      let index = this.getIndexById(id);
      if (index !== -1) {
        this.tasks.splice(index, 1);
        localStorage.setItem("tasks", JSON.stringify(this.tasks));
      }
    }
  }
}
</script>

<style scoped>

</style>

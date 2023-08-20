<template>
    <li :class="{ checked: isDone }" class="task" @click="todoCheck">{{ label }}
      <span @click="startEditing" class="edit"><i class="fa fa-light fa-pencil"></i></span>
      <span class="close" @click="todoDelete"><i class="fa fa-times"></i></span>
    </li>
  </template>
  
  <script>
  export default {
    props: {
      label: String,
      done: Boolean,
      id: Number,
    },
    data() {
      return {
        isDone: this.done,
      };
    },
    methods: {
      todoDelete() {
        this.$emit('todo-delete', this.id);
      },
      todoCheck() {
        this.$emit('todo-check', this.id);
        this.isDone = !this.isDone;
      },
      startEditing() {
        this.$emit('start-editing', this.id);
      },
    },
    emits: ['todo-delete', 'todo-check', 'start-editing'],
  };
  </script>
  
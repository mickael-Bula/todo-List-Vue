<template>
  <div class="task">
    <input
      ref="editTask"
      type="text"
      class="input-edit"
      id="myInput-item"
      :placeholder="editingLabel"
      v-model.trim="editingLabel"
      @keydown.enter.prevent="onSubmit"
    >
    <span @click.prevent="onSubmit" class="edit"><i class="fa fa-sharp fa-solid fa-check"></i></span>
    <span class="close" @click="editCancelled"><i class="fa fa-light fa-rotate-left"></i></span>
  </div>
</template>

<script>
export default {
  props: {
    label: String,
    id: Number,
  },
  mounted() {
        const editTask = this.$refs.editTask;
        editTask.focus();
    },
  data() {
    return {
      editingLabel: this.label,
    };
  },
  methods: {
    onSubmit() {
      if (this.editingLabel === '') {
        return;
      }
      this.$emit('todo-edit', this.id, this.editingLabel);
    },
    editCancelled() {
      this.isEditing = false;
      this.$emit('edit-cancelled', this.id, this.isEditing, this.label);
    }
  },
  emits: ['todo-edit', 'edit-cancelled'],
};
</script>

<style>

</style>
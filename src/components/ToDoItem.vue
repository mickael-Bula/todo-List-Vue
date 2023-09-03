<template>
    <li v-if="!isEditing" :class="{ checked: isDone }" class="task" @click="todoCheck">{{ label }}
        <span @click="startEditing" class="edit"><i class="fa fa-light fa-pencil"></i></span>
        <span class="close" @click="todoDelete"><i class="fa fa-light fa-pencil"></i></span>
    </li>
    <form v-else id="form-item" class="form">
        <input type="text" class="input" id="myInput-item" @keydown.enter.prevent @keyup.enter="onSubmit" v-model.trim="editingLabel">
        <button class="addBtn" @click.prevent="onSubmit">Modifier</button>
  </form>
</template>

<script>
export default {
    // props permet d'accéder aux propriétés (attributs) déclarées dans le parent
    props: {
        label: {
            required: true,
            type: String
        },
        done: {
            default: false,
            type: Boolean
        },
        id: {
            required: true,
            type: Number
        }
    },
    data() {
        return {
            isDone: this.done,
            isEditing: false,
            editingLabel: this.label,
        }
    },
    methods: {
        startEditing() {
            this.isEditing = true;
        },
        onSubmit(event) {
            event.stopPropagation();
            if (this.editingLabel === "") {
                return;
            }
            this.$emit('todo-edit', this.id, this.editingLabel);
            this.isEditing = false;
        },
        todoDelete() {
            // event.stopPropagation(); // ne convient pas car empêche l'évènement d'être propagé au parent qui doit pourtant exécuter la méthode déclarée dans $emit()
            console.log("dans todo-delete");
            this.$emit('todo-delete', this.id);
        },
        todoCheck() {
            // event.stopPropagation();
            console.log("dans todo-check");
            this.$emit('todo-check', this.id);
            this.isDone = !this.isDone;
        },
    },
    emits: ['todo-delete', 'todo-edit', 'todo-check'],
};
</script>

<style>

</style>
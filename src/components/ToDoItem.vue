<template>
    <!-- <div>
        <label for="todo-item">{{ label }}</label>
        <input type="checkbox" id="todo-item" :checked="isDone">
    </div> -->
    <div :checked="isDone">{{ label }}<span class="close" @click="todoDelete">{{ '\u00D7' }}</span></div>
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
        }
    },
    methods: {
        todoDelete() {
            this.$emit('todo-delete', this.id);
        }
    },
    emits: ['todo-delete'],
};
</script>

<style>

</style>
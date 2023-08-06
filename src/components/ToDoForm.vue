<template>
    <form id="form" class="form">
      <input type="text" class="input" id="myInput" placeholder="Titre..." v-model.lazy.trim="label">
      <button class="addBtn" @click.prevent="onSubmit">Ajouter</button>
    </form>
</template>

<script>
export default {
    data() {
        return {
            label: "",
        }
    },
    methods: {
        onSubmit() {
            if ("" == this.label) {
                return
            };
            this.$emit('todo-added', this.label);
            console.log("emit est passé");
            this.label="";
        }
    },
    emits: ['todo-added'],
}
</script>
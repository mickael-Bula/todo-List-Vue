<template>
    <form id="form" class="form">
      <input type="text" class="input" id="myInput" @keyup.enter="onSubmit" placeholder="Titre..." v-model.trim="label">
      <button class="addBtn" @click.self.prevent="onSubmit">Ajouter</button>
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
            console.log("dans onSubmit");
            this.$emit('todo-added', this.label);
            this.label="";
        }
    },
    emits: ['todo-added'],
}
</script>
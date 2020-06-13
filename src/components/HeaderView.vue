<template>
    <div class="container-fluid">
        <img alt="elma-flow" src="../assets/logo.png">
        <button class="btn btn-default" @click="addNewUser"><i class="fa fa-user-plus"></i></button>
        <button class="btn btn-default" @click="editUser"><i class="fa fa-server"></i></button>
    </div>
</template>

<script>
export default {
    methods: {
        addNewUser() {
            this.$emit('addNewUser');
        },
        editUser() {
            this.$emit('editUser');
        }
    }
}
</script>

<style lang="scss" scoped>
 div {
     background-color: #222222;
     height: 5vh;
     img {
         height: 100%;
         width: auto;
     }
     button {
         color: white;
         float: right;
     }
     button:hover {
         color: rgba(255, 106, 0, 0.692);
     }
 }
</style>
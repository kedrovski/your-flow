<template>
    <div>
        <h4>{{title}}</h4>
        <button @click="add"><i class="fa fa-plus"></i></button>
    </div>
</template>

<script>
export default {
    props: {
        title: {
            type: String,
            required: true
        },
    },
    methods: {
        add() {
            this.$emit('addNewTask', this.title);
        }
    }
}
</script>

<style lang="scss" scoped>
    div {
        width: 100%;
        background-color: #dfdfdfc5;
        padding: 10px;
        h4 {
            display: inline;
            font-size: 1rem;
            font-weight: 600;
        }
        button {
            font-weight: bold;
            background-color: transparent;
            border: none;
            font-size: 1.6rem;
            color: #3dc54e;
            position: absolute;
            right: 5px;
            top: 2px;
        }
        button:hover {
            color: rgba(255, 106, 0, 0.692);
        }
    }
</style>
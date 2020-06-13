<template>
<div>
    <div class="card">
        <div class="card-header">
            <h5 class="card-title">{{task.title}}</h5>
            <button class="btn edit" @click="eventEdit(task.id)"><i class="fa fa-pencil"></i></button>
            <button class="btn delete" @click="eventDelete(task.id)"><i class="fa fa-close"></i></button>
        </div>
        <div class="card-body">
            <p class="card-text" v-if="task.isTypeSimple">{{task.description || 'There is no description'}}</p>
            <div v-else>
                <ul>
                    <li v-for="(item, index) of task.items" :key="index">
                        <i class="fa fa-circle"></i> {{item}}
                    </li>
                </ul>
            </div>
            <div class="image-block" v-for="(user, index) of task.selectedUsers" :key="index">
                <img :src="getImgUrl(user.avatar)" :alt="user.name" :title="user.name">
            </div>
        </div>
    </div>
</div>
</template>

<script>
export default {
    props: {
        task: {
            type: Object,
            required: true
        }
    },
    mounted() {
        console.log(this.task)
    },
    methods: {
        getImgUrl(image) {
            let images = require.context('../assets/avatars/', false, /\.png$/)
            return images('./' + image)
        },
        eventDelete(id) {
            this.$emit('delete', id);
        },
        eventEdit(id) {
            this.$emit('edit', id);
        }
    }
}
</script>

<style lang="scss" scoped>
.card {
    cursor: pointer;
    .card-header {
        padding: 10px !important;
        height: 42px !important;

        button.delete {
            position: absolute;
            padding: 0;
            font-size: 17px;
            top: 6px;
            right: 10px;
            color: #ff00006e;
            background-color: transparent;
        }
        button.delete:hover {
            color: #ff0000c9;
        }

        button.edit {
            position: absolute;
            font-size: 14px;
            padding: 0;
            top: 8px;
            right: 35px;
            color: #0077ff86;
            background-color: transparent;
        }
        button.edit:hover {
            color: #0077ff;
        }
    }
    .card-title {
        font-size: 1rem !important;
        width: 80%;
        white-space: nowrap;
        overflow: hidden;
        font-size: 1rem !important;
        text-overflow: ellipsis;
    }
    .image-block {
        display: inline-block;
        padding-right: 7px;
        img {
            width: 35px;
            height: auto;
            display: inline-block;
        }
    }
    ul {
        padding-left: 0px;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        list-style: none;
        .fa {
            font-size: 8px;
            position: relative;
            top: -3px;
            right: 0px;
            color: gray;
            padding-right: 5px;
        }
    }
}


</style>
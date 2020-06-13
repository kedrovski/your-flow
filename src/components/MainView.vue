<template>
<div class="container-fluid columns-view">
    <HeaderView @addNewUser="openAddUserModal" @editUser="openEditUserModal"/>
    <AddUserModal v-model="isUserModalVisible" :edit="isEditMode" :users="usersList" :avatars="avatars" @close="closeAddUserModal" @userAdded="userAdded" @userEdited="userEdited" @deleteUser="deleteUser"/>
    <AddTaskModal v-model="isTaskModalVisible" :users="usersList" :groupType="taskGroupType" :existedTask="selectedTask" @close="closeAddTaskModal" @taskAdded="taskAdded" @taskEdited="taskEdited" />
    <div class="row">
        <div class="col-md-3 column-tab bordered-1">
            <div class="column-header">
                <HeaderColumn :title="'To-do'" @addNewTask="openAddTaskModal"/>
            </div>
            <draggable class="column-body" :list="tasksTodo" group="people" @change="dragCards">
                <div class="mb-3" v-for="(task, index) of tasksTodo" :key="index">
                    <TaskView :task="task" @edit="editTask" @delete="deleteTask"/>
                </div>
            </draggable>
        </div>

        <div class="col-md-3 column-tab bordered-2">
            <div class="column-header">
                <HeaderColumn :title="'Do today'" @addNewTask="openAddTaskModal"/>
            </div>
            <draggable class="column-body" :list="tasksToday" group="people" @change="dragCards">
                <div class="mb-3" v-for="(task, index) of tasksToday" :key="index">
                    <TaskView :task="task" @edit="editTask" @delete="deleteTask"/>
                </div>
            </draggable>
        </div>

        <div class="col-md-3 column-tab bordered-2">
            <div class="column-header">
                <HeaderColumn :title="'In progress'" @addNewTask="openAddTaskModal"/>
            </div>
            <draggable class="column-body" :list="tasksInProgress" group="people" @change="dragCards">
                <div class="mb-3" v-for="(task, index) of tasksInProgress" :key="index">
                    <TaskView :task="task" @edit="editTask" @delete="deleteTask"/>
                </div>
            </draggable>
        </div>

        <div class="col-md-3 column-tab bordered-2">
            <div class="column-header">
                <HeaderColumn :title="'Done'" @addNewTask="openAddTaskModal"/>
            </div>
            <draggable class="column-body" :list="tasksDone" group="people" @change="dragCards">
                <div class="mb-3" v-for="(task, index) of tasksDone" :key="index">
                    <TaskView :task="task" @edit="editTask" @delete="deleteTask"/>
                </div>
            </draggable>
        </div>
    </div>
</div>
</template>

<script>
import HeaderView from './HeaderView.vue'
import HeaderColumn from './HeaderColumn.vue'
import AddTaskModal from './AddTaskModal.vue'
import AddUserModal from './AddUserModal.vue'
import TaskView from './TaskView.vue'
import draggable from 'vuedraggable'
export default {
    components: {
        HeaderView,
        HeaderColumn,
        AddTaskModal,
        AddUserModal,
        TaskView,
        draggable,
    },
    data() {
        return {
            isTaskModalVisible: false,
            isUserModalVisible: false,
            usersList: [],
            avatars: [
                '64_1.png',
                '64_2.png',
                '64_3.png',
                '64_4.png',
                '64_5.png',
                '64_6.png',
                '64_7.png',
                '64_8.png',
                '64_9.png',
                '64_10.png',
                '64_11.png',
                '64_12.png',
                '64_13.png',
                '64_14.png',
                '64_15.png',
            ],
            tasks: [],
            tasksTodo: [],
            tasksToday: [],
            tasksInProgress: [],
            tasksDone: [],
            taskGroupType: 'To-do',
            selectedTask: {},
            isEditMode: false,
        }
    },
    mounted() {
        if (localStorage.tasks) {
            this.tasks = JSON.parse(localStorage.tasks);
            this.filterTasks();
        }
        if (localStorage.users) {
            this.usersList = JSON.parse(localStorage.users);
        }
    },
    methods: {
        openAddTaskModal(data) {
            this.taskGroupType = data;
            this.isTaskModalVisible = true;
        },
        openAddUserModal() {
            this.isUserModalVisible = true;
        },
        openEditUserModal() {
            this.isEditMode = true;
            this.isUserModalVisible = true;
        },
        closeAddTaskModal() {
            this.isTaskModalVisible = false;
            this.selectedTask = {};
        },
        closeAddUserModal() {
            this.isEditMode = false;
            this.isUserModalVisible = false;
        },
        taskAdded(data) {
            this.tasks.push(data);
            this.saveTasksToStorage();
        },
        taskEdited(data) {
            let index = this.tasks.findIndex(task => task.id === data.id);
            this.tasks[index] = data;
            this.saveTasksToStorage();
        },
        userAdded(data) {
            this.usersList.push(data);
            this.saveUsersToStorage();
        },
        userEdited(data) {
            let index = this.usersList.findIndex(u => u.id === data.id);
            this.usersList[index] = data;
            this.saveUsersToStorage();
        },
        deleteUser(id) {
            this.usersList = this.usersList.filter(u => u.id !== id);
            this.saveUsersToStorage();
        },
        filterTasks() {
            this.tasksTodo = this.tasks.filter((task) => task.groupType === 'To-do');
            this.tasksToday = this.tasks.filter((task) => task.groupType === 'Do today');
            this.tasksInProgress = this.tasks.filter((task) => task.groupType === 'In progress');
            this.tasksDone = this.tasks.filter((task) => task.groupType === 'Done');
        },
        editTask(id) {
            this.selectedTask = this.tasks.filter((task) => task.id === id)[0];
            this.openAddTaskModal();
        },
        deleteTask(id) {
            this.tasks = this.tasks.filter((task) => task.id !== id);
            this.saveTasksToStorage();
        },
        saveTasksToStorage() {
            localStorage.setItem('tasks', JSON.stringify(this.tasks));
            this.filterTasks();
        },
        saveUsersToStorage() {
            localStorage.setItem('users', JSON.stringify(this.usersList));
        },
        dragCards() {
            this.tasksTodo.forEach(v => v.groupType = 'To-do');
            this.tasksInProgress.forEach(v => v.groupType = 'In progress');
            this.tasksToday.forEach(v => v.groupType = 'Do today');
            this.tasksDone.forEach(v => v.groupType = 'Done');
            this.tasks = [...this.tasksTodo, ...this.tasksInProgress, ...this.tasksToday, ...this.tasksDone];
            this.saveTasksToStorage();
        }
    }
}
</script>

<style lang="scss" scoped>
.columns-view {
    background-color: #dfdfdfc5;
    height: 100vh;
    padding: 20px;

    .row {
        margin: 0;
    }

    .column-tab {
        padding: 0px;
        // height: calc(100vh - 5vh - 20px - 20px);
        background-color: #ffffff;
        .column-header {
            border-bottom: 1px solid gray;
        }
        .column-body {
            padding: 15px;
            overflow-y: auto;
            height: calc(100vh - 11vh - 20px - 20px);
        }
    }

    .bordered-1 {
        border-left: 1px solid gray;
        border-right: 1px solid gray;
        border-bottom: 1px solid gray;

    }
    .bordered-2 {
        border-right: 1px solid gray;
        border-bottom: 1px solid gray;
    }
}

@media only screen and (max-width: 768px) {
    .row { 
        display: block !important;
    }
    .columns-view {
        height: auto !important;
        padding: 0 !important;
    }
    .bordered-1,
    .bordered-2 {
        border: 1px solid gray;
    }
}
</style>
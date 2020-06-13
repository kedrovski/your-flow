<template>
<div class="container-fluid">
    <div tabindex="-1" class="modal fade" id="taskModal" role="dialog" aria-labelledby="modalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
            <div class="modal-body">
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                </button>
                <input type="text" class="form-control mb-3 mt-3" v-model="task.title" aria-describedby="inputGroup-sizing-default" placeholder="Task name.." autofocus>
                <button class="btn btn-default mb-3" @click="showDescription"><i class="fa fa-file-text-o"></i> Descripiton</button>
                <button class="btn btn-default mb-3 ml-2 mr-2" @click="showItemsList"><i class="fa fa-list-ol"></i> Items</button>
                <button class="btn btn-default mb-3" @click="showMembersList"><i class="fa fa-user-o"></i> Members</button>
                <div class="form-group" v-if="isDescriptionVisible && !isItemsListVisible">
                    <label for="textareaDescription">Description</label>
                    <textarea class="form-control" v-model="task.description" id="textareaDescription" rows="3" placeholder="Please enter task description.."></textarea>
                </div>
                <div class="form-group" v-if="isItemsListVisible && !isDescriptionVisible">
                    <label>Items</label>
                    <div class="card">
                        <div class="row">
                            <div class="col-md-8">
                                <input type="text" class="form-control" v-model="itemName" aria-describedby="inputGroup-sizing-default" placeholder="Item name..">
                            </div>
                            <div class="col-md-4 add-item-block">
                                <button class="btn btn-info btn-add-item" @click="addNewItem">Add item</button>
                            </div>
                        </div>
                        <div class="list-block mt-3">
                            <ul v-if="task.items && task.items.length">
                                <li v-for="(item, index) of task.items" :key="index">{{item}}</li>
                            </ul>
                            <p v-else>No items yet..</p>
                        </div>
                    </div>
                </div>
                <div class="form-group" v-if="isMembersListVisible">
                    <label for="exampleFormControlTextarea1">Members</label>
                    <div class="card members-block">
                        <div class="checkbox" v-for="(user, index) of users" :key="index">
                            <label><input type="checkbox" :value="user" v-model="task.selectedUsers">{{user.name}}</label>
                        </div>
                    </div>
                </div>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Cancel</button>
                <button type="button" class="btn btn-success" @click="saveTask">Save changes</button>
            </div>
            </div>
        </div>
    </div>
</div>
</template>

<script>
import $ from 'jquery';
export default {
    props: {
        value: {
            type: Boolean,
            required: true,
        },
        users: {
            type: Array,
            required: true,
        },
        groupType: {
            type: String,
            required: false,
        },
        existedTask: {
            type: Object,
            required: true,
        }
    },
    mounted() {
        $('#taskModal').on('hidden.bs.modal', () => {
            this.outsideClose();
        });
    },
    watch: {
        value(val) {
            if (val) {
                this.showModal();
            } else {
                $('#taskModal').modal('hide');
            }
        },
        existedTask(data) { 
            if (data && data.groupType) {
                this.task = data;
                this.taskType = data.groupType;
                this.editMode = true;
            } else {
                this.editMode = false;
            }
        },
    },
    data() {
        return {
            isDescriptionVisible: false,
            isMembersListVisible: false,
            isItemsListVisible: false,
            task: {
                title: '',
                description: '',
                items: [],
                selectedUsers: []
            },
            itemName: '',
            id: '',
            editMode: false,
        }
    },
    methods: {
        showModal() {
            $('#taskModal').modal('show');
        },
        outsideClose() {
            this.clearFields();
            this.$emit('close');
        },
        showDescription() {
            this.isDescriptionVisible = !this.isDescriptionVisible;
            this.isItemsListVisible = false;
        },
        showMembersList() {
            this.isMembersListVisible = !this.isMembersListVisible;
        },
        showItemsList() {
            this.isItemsListVisible = !this.isItemsListVisible;
            this.isDescriptionVisible = false;
        },
        addNewItem() {
            if (this.itemName) {
                this.task.items.push(this.itemName);
                this.itemName = '';
            }
        },
        saveTask() {
            let currentTask = {
                title: this.task.title,
                description: this.task.description,
                items: this.task.items,
                selectedUsers: this.task.selectedUsers,
                isTypeSimple: this.isDescriptionVisible || (!this.isDescriptionVisible && !this.isItemsListVisible),
            }
            if (!this.editMode) {
                currentTask = Object.assign(currentTask, { groupType: this.groupType, id: Math.random().toString().substr(2)});
                this.$emit('taskAdded', currentTask);
            } else {
                currentTask = Object.assign(currentTask, { groupType: this.taskType , id: this.task.id });
                this.$emit('taskEdited', currentTask);
            }
            this.clearFields();
            $('#taskModal').modal('hide');
        },
        clearFields() {
            this.task = {
                title: '',
                description: '',
                items: [],
                selectedUsers: []
            };
            this.itemName = '';
        },
    },
}
</script>

<style lang="scss" scoped>
    .btn-default {
        border: 1px solid #ced4da !important;
        .fa {
            padding-right: 5px;
        }
    }
    .btn-default:hover {
        border: 1px solid gray !important;
    }
    .close {
        position: absolute;
        top: 2px;
        right: 8px;
    }
    .modal-footer {
        border-top: none !important;
        padding-top: 0 !important;
    }
    .modal-body {
        padding-bottom: 10px !important;
    }
    .members-block{
        max-height: 125px;
        overflow-y: auto;
    }
    .card {
        padding: 15px;
    }
    ul {
        padding-left: 20px;
    }
    .checkbox input {
        margin-right: 8px;
    }
    .btn-add-item {
        float: left;
        width: 100%;
    }
    .add-item-block {
        padding-left: 0;
    }
    .list-block {
        max-height: 100px;
        overflow-y: auto;
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
        .add-item-block {
            padding-top: 10px;
            padding-left: 15px;
        }
    }
</style>
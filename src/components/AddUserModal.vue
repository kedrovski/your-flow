<template>
<div class="container-fluid">
    <div tabindex="-1" class="modal fade" id="userModal" role="dialog" aria-labelledby="modalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
            <div class="modal-body">
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                </button>

                <div v-if="!isEditMode">
                    <input type="text" class="form-control mb-3 mt-3" v-model="userName" placeholder="User name..">
                    <div class="form-group">
                        <label for="selectAvatarBlock">Select avatar</label>
                        <div class="card avatar-block" id="selectAvatarBlock">
                            <div class="avatar-select" :class="isAvatarSelected(avatar)" v-for="(avatar, index) of avatars" :key="index">
                                <img :src="getImgUrl(avatar)" @click="selectAvatar(avatar)">
                            </div>
                        </div>
                    </div>
                </div>

                <div v-if="isEditMode">
                    <label for="selectAvatarBlock">Select user for edit</label>
                    <div class="card select-user mt-3">
                        <div class="col-md-4 user" v-for="(user, index) of users" :key="index" @click="selectUser(user.id)">
                            <img :src="getImgUrl(user.avatar)">
                            {{user.name}}
                        </div>
                    </div>
                </div>
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-danger btn-delete" @click="deleteUser" v-if="isEditStep2"><i class="fa fa-trash-o"></i> Delete</button>
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Cancel</button>
                <button type="button" class="btn btn-success" @click="editUser" v-if="isEditStep2">Edit user</button>
                <button type="button" class="btn btn-success" @click="saveUser" v-else>Add new user</button>
            </div>
            </div>
        </div>
    </div>
</div>
</template>

<script>
import $ from 'jquery';
export default {
    mounted() {
        $('#userModal').on('hidden.bs.modal', () => {
            this.outsideClose();
        });
        this.selectedAvatar = this.avatars[0];
    },
    props: {
        value: {
            type: Boolean,
            required: true,
        },
        avatars:  {
            type: Array,
            required: true,
        },
        edit: {
            type: Boolean,
            required: true,
        },
        users: {
            type: Array,
            required: true,
        },
    },
    watch: {
        value(val) {
            if (val) {
                this.showModal();
            } else {
                $('#userModal').modal('hide');
            }
        },
        edit(data) {
            this.isEditMode = data;
            this.isEditStep2 = false;
        },
    },
    data() {
        return {
            userId: '',
            userName: '',
            selectedAvatar: '',
            isEditMode: false,
            isEditStep2: false,
        }
    },
    methods: {
        showModal() {
            $('#userModal').modal('show');
        },
        outsideClose() {
            this.clearFields();
            this.$emit('close');
        },
        saveUser() {
            if (this.userName) {
                this.$emit('userAdded', {
                    id: Math.random().toString().substr(2),
                    name: this.userName,
                    avatar: this.selectedAvatar,
                });
                this.clearFields();
            }
            $('#userModal').modal('hide');
        },
        editUser() {
            if (this.userName) {
                this.$emit('userEdited', {
                    id: this.userId,
                    name: this.userName,
                    avatar: this.selectedAvatar,
                });
                this.clearFields();
            }
            $('#userModal').modal('hide');
        },
        deleteUser() {
            this.$emit('deleteUser', this.userId);
            this.clearFields();
            $('#userModal').modal('hide');
        },
        clearFields() {
            this.userName = '';
            this.selectedAvatar = this.avatars[0];
        },
        getImgUrl(image) {
            let images = require.context('../assets/avatars/', false, /\.png$/)
            return images('./' + image)
        },
        isAvatarSelected(avatar) {
            return {
                'selected': this.selectedAvatar === avatar ? true : false
            }
        },
        selectAvatar(avatar) {
            this.selectedAvatar = avatar;
        },
        selectUser(id) {
            let user = this.users.filter((u) => u.id === id)[0];
            this.userId = user.id;
            this.userName = user.name;
            this.selectedAvatar = user.avatar;
            this.isEditMode = false;
            this.isEditStep2 = true;
        },
    },
}
</script>

<style lang="scss" scoped>
    .btn-delete {
        position: absolute;
        left: 12px;
        bottom: 12px;
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
    .card {
        padding: 15px;
    }
    .avatar-block {
        display: block;
        .avatar-select {
            cursor: pointer;
            width: 80px;
            height: 75px;;
            text-align: center;
            padding: 5px;
            display: inline-block;
        }
        .avatar-select:hover,
        .selected {
            cursor: pointer;
            width: 80px;
            height: 75px;;
            text-align: center;
            padding: 5px;
            display: inline-block;
            background-color: rgba(127, 255, 212, 0.685);
            border-radius: 0.25rem;
        }
    }
    .select-user {
        width: 100%;
        height: 150px;
        overflow-y: auto;
        display: inline-block;
        .user {
            cursor: pointer;
            display: inline-block;
            padding: 5px 10px;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
            img {
                margin-right: 5px;
                width: 45px;
                height: auto;
            }
        }
        .user:hover {
            background-color: rgba(127, 255, 212, 0.685);
            border-radius: 0.25rem;
        }
    }
</style>
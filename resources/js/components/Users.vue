<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-12">
                <div class="bd-example">
                    <div class="table-responsive-sm">
                        <button class="btn btn-success float-right mt-1 mb-1" data-toggle="modal" data-target="#addnew"> <i class="fas fa-user-plus"></i> Add New</button>
                            <table class="table table-bordered mt-5">
                                <thead>
                                <tr>
                                    <th scope="col">Id</th>
                                    <th scope="col">Name</th>
                                    <th scope="col">Email</th>
                                    <th scope="col">Type</th>
                                    <th scope="col">Registered at</th>
                                    <th scope="col">Modify</th>
                                </tr>
                                </thead>
                                <tbody>
                                <tr v-for="user in users" :key="user.id">
                                
                                    <td>{{user.id}}</td>
                                    <td>{{user.name}}</td>
                                    <td>{{user.email}}</td>
                                    <td>{{user.type | upText}}</td>
                                    <td>{{user.created_at | createdDate }}</td>
                                    <td>
                                        <a href="" class="edit_icon"><i class="far fa-edit"></i></a>
                                        <a href="" class="delete_icon"><i class="far fa-trash-alt"></i></a>

                                    </td>
                                
                                </tr>
                                
                                
                                </tbody>
                            </table>
                        </div>
                </div>
            </div>
        </div>

        <!-- Modal -->
        <div class="modal fade" id="addnew" tabindex="-1" role="dialog" aria-labelledby="addnewLabel" aria-hidden="true">
        <div class="modal-dialog modal-dialog-centered" role="document">
            <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title" id="addnewLabel">Add New User</h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
                </button>
            </div>
            <form @submit.prevent = "createUser" >
            <div class="modal-body">

                <div class="form-group">
                    <input v-model="form.name" type="text" name="name"
                        class="form-control" :class="{ 'is-invalid': form.errors.has('name') }" placeholder="Name">
                    <has-error :form="form" field="name"></has-error>
                </div>

                <div class="form-group">
                    <input v-model="form.email" type="email" name="email"
                        class="form-control" :class="{ 'is-invalid': form.errors.has('name') }" placeholder="Email">
                    <has-error :form="form" field="email"></has-error>
                </div>

                <div class="form-group">
                    <input v-model="form.bio" type="text" name="bio"
                        class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }" placeholder="Short Bio">
                    <has-error :form="form" field="bio"></has-error>
                </div>

                <div class="form-group"> 
                    <select name="type" v-model="form.type" id="" class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                      <option value="">Select User Type</option>
                      <option value="admin">Admin</option>
                      <option value="user">User</option>
                      <option value="auther">Author</option>
                    </select>
                    <has-error :form="form" field="type"></has-error>
                </div>

                <div class="form-group">
                    <input v-model="form.password" type="password" name="password"
                        class="form-control" :class="{ 'is-invalid': form.errors.has('password') }" placeholder=" Password">
                    <has-error :form="form" field="password"></has-error>
                </div>


            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                <button type="submit" class="btn btn-primary">Save</button>
            </div>
            </form>
            </div>
        </div>
        </div>


    </div>
</template>

<script>
    export default {
        data(){
            return{
                users:{},
                form: new Form({
                    name : '',
                    email : '',
                    password : '',
                    type : '',
                    bio : '',
                    photo: ''
                })
            }
        },
        methods:{
            loadUser(){
                axios.get('api/user').then(({data}) => (this.users = data.data));
            },
          createUser(){
              this.$Progress.start();
              this.form.post('api/user');
              this.$Progress.finish();
          }
        },
        created() {
            this.loadUser();
        }
    }
</script>

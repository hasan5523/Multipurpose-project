<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-12">
                <div class="bd-example">
                    <div class="table-responsive-sm">
                        <button class="btn btn-success float-right mt-1 mb-1" @click="newModal"> <i class="fas fa-user-plus"></i> Add New</button>
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
                                        <a href="" class="edit_icon" @click.prevent = "editModal(user)"><i class="far fa-edit"></i></a>
                                        <a href="" class="delete_icon" @click.prevent="deleteUser(user.id)"><i class="far fa-trash-alt"></i></a>

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
                <h5 v-show="editMode" class="modal-title" id="addnewLabel">Update User Info</h5>
                <h5 v-show="!editMode"  class="modal-title" id="addnewLabel">Add New User</h5>

                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
                </button>
            </div>
            <form @submit.prevent = "editMode ? updateUser(): createUser()" >
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
                
                <button v-show="editMode" type="submit" class="btn btn-success">Update</button>
                <button v-show="!editMode" type="submit" class="btn btn-primary">Save</button>

            </div>
            </form>
            </div>
        </div>
        </div>


    </div>
</template>

<script>
import { setInterval } from 'timers';
    export default {
        data(){
            return{
                editMode: false,
                users:{},
                form: new Form({
                    id:'',
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
            updateUser(){
                 this.$Progress.start();
                 this.form.put('api/user/'+ this.form.id)
                 .then( ()=>{
                     $("#addnew").modal('hide');
                     Swal.fire(
                        'Updated!',
                        'Your info is updated.',
                        'success'
                        )

                    this.$Progress.finish();
                    Fire.$emit('afterCreate');
                 })
                 .catch(()=>{
                      this.$Progress.fail();
                 });
            },
            newModal(){
                this.editMode = false;
                this.form.reset();
                $("#addnew").modal('show');
            },

            editModal(user){
                this.editMode = true;
                this.form.reset();
                $("#addnew").modal('show');
                this.form.fill(user);
            },

            deleteUser(id){
                Swal.fire({
                    title: 'Are you sure?',
                    text: "You won't be able to revert this!",
                    type: 'warning',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Yes, delete it!'
                    }).then((result) => {

                            if (result.value) {
                                this.form.delete('api/user/'+ id).then(()=>{
                                Swal.fire(
                                    'Deleted!',
                                    'Your file has been deleted.',
                                    'success'
                                    )

                                    Fire.$emit('afterCreate');
                                });
                            }
                    })

            },
            loadUser(){
                axios.get('api/user').then(({data}) => (this.users = data.data));
            },
          createUser(){
              this.$Progress.start();
              this.form.post('api/user')
              .then(()=>{
                Fire.$emit('afterCreate');
                $("#addnew").modal('hide');
                Toast.fire({
                  type: 'success',
                  title: 'User Created successfully'
                })
                this.$Progress.finish();
              });
              
          }
        },
        created() {
            this.loadUser();
            Fire.$on('afterCreate', () => {
                this.loadUser();
            });

            //setInterval(()=>this.loadUser(),3000);
        }
    }
</script>

<template>
  <div>
    <h2 class="center">User List</h2>

    <form @submit.prevent="addUser()" class="mb-3">
      <div class="form-group">
        <input type="text" class="form-control" placeholder="Full Name" v-model="user.fullname">
      </div>
      <div class="form-group">
        <input type="email" class="form-control" placeholder="Email" v-model="user.email"/>
      </div>
      <div class="form-group">
        <input type="text" class="form-control" placeholder="Phone Number (international format, e.g. +420 128 2381 101)" v-model="user.number">
      </div>
      <button type="submit" class="btn btn-primary">Save</button>
    </form>
    
    <table class="table mb-5">
      <thead>
        <tr>
          <th>ID</th>
          <th>Full Name</th>
          <th>Email</th>
          <th>Phone number</th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in users" v-bind:key="user.id">
          <td>{{user.id}}</td>
          <td>{{user.fullname}}</td>
          <td>{{user.email}}</td>
          <td>{{user.number}}</td>
          <td><button class="btn btn-primary" @click="editUser(user)">Edit User Info</button></td>
          <td><button class="btn btn-danger" @click="deleteUser(user.id)">Delete User</button></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        users: [],
        user: {
          id: '',
          fullname: '',
          email: '',
          phonenumber: ''
        },
        editFlag: false
      }
    },

    created() {
      this.fetchUsers();
    },

    methods: {
      fetchUsers(userUrl) {
        let vm = this;
        userUrl = userUrl || '/api/users'
        fetch(userUrl)
          .then(res => res.json())
          .then(res => {
            this.users = res.data;
          })
          .catch(err => console.error(err));
      },
      deleteUser(id) {
        if(confirm("Are you sure you want to delete this user?")) {
          fetch(`api/user/${id}`, {
            method: 'delete'
          })
          .then(res => res.json)
          .then(data => {
            alert('User deleted successfully!');
            this.fetchUsers();
          })
          .catch(err => console.error(err));
        }
      },
      addUser() {
        if(this.editFlag) {
          fetch('api/user', {
            method: 'put',
            body: JSON.stringify(this.user),
            headers: {
              'content-type': 'application/json'
            }
          })
          .then(res => res.json())
          .then(data => {
            this.user.fullname = '';
            this.user.email = '';
            this.user.number = '';
            alert('User changed successfully!');
            this.fetchUsers();
          })
          .catch(err => console.error(err));
          
          this.editFlag = false;
        } else {
          fetch('api/user', {
            method: 'post',
            body: JSON.stringify(this.user),
            headers: {
              'content-type': 'application/json'
            }
          })
          .then(res => res.json())
          .then(data => {
            this.user.fullname = '';
            this.user.email = '';
            this.user.number = '';
            alert('User added successfully!');
            this.fetchUsers();
          })
          .catch(err => console.error(err));
        }
      },
      editUser(user) {
        this.editFlag = true;
        this.user.id = user.user_id;
        this.user.user_id = user.id;
        this.user.fullname = user.fullname;
        this.user.email = user.email;
        this.user.number = user.number;
      }
    }
  }
</script>

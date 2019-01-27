<template>
  <div id="Signup">
    <div class="row my-5">
      <div class="col-md-8 offset-md-2">
        <div class="card">
          <div class="card-body">
            <h3>Signup</h3>
            <div class="form-group">
              <input v-bind:class="{'is-invalid': errors.name, 'is-valid': !errors.name && submitted}" v-model="name" type="text" placeholder="Username" class="form-control">
              <div class="errors" v-if="errors.name">
                <small class="text-danger" :ley="error" v-for="error in errors.name">{{error}}</small>
              </div>
            </div>
            <div class="form-group">
              <input v-bind:class="{'is-invalid': errors.email, 'is-valid': !errors.email && submitted}" v-model="email" type="text" placeholder="Email" class="form-control">
              <div class="errors" v-if="errors.email">
                <small class="text-danger" :ley="error" v-for="error in errors.email">{{error}}</small>
              </div>
            </div>
            <div class="form-group">
              <input v-model="password" type="password" placeholder="Password" class="form-control">
              <div v-bind:class="{'is-invalid': errors.password, 'is-valid': !errors.password && submitted}" class="errors" v-if="errors.password">
                <small class="text-danger" :ley="error" v-for="error in errors.password">{{error}}</small>
              </div>
            </div>
            <div class="form-group text-center">
              <button @click="registerUser()" :disabled="loading" class="btn form-control btn-success">
                <i v-if="loading" class="fas fa-spin fa-spinner"></i>
                {{loading ? '': 'Signup'}}
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import Axios from 'axios';
import config from '@/config.js';

export default {
  beforeRouteEnter(to, from, next) {
    if (localStorage.getItem('auth')){
      return next({path: '/'});
    }
    next();
  },
  data: () => ({
    name: '',
    email: '',
    password: '',
    errors: {},
    submitted: false,
    loading: false
  }),
  methods: {
    registerUser(){
      this.loading = true
      Axios.post(`${config.apiUrl}/auth/register`, {
        name: this.name,
        email: this.email,
        password: this.password
      }).then(response => {
        this.submitted = true
        this.loading = false
        this.$noty.success('Successfully registered')
        localStorage.setItem('auth', JSON.stringify(response.data.data))
        this.$root.auth = response.data.data;

        this.$router.push('/');
      }).catch(response => {
        this.submitted = true
        this.loading = false
        this.$noty.error('Opps Something went wrong!')
        this.errors = response.response.data
      });
    }
  }
}
</script>
<style lang="scss" scoped>
</style>

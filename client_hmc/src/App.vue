<script>
  import {loginUrl, getHeaders, userUrl} from './config'
  import {clientId, clientSecret} from './env'
  export default {
    data () {
      return {
        login: {
          email: 'tfarmer4@gmail.com',
          password: 'password'
        }
      }
    },
    methods: {
      handleLoginFormSubmit () {
        const postData = {
          grant_type: 'password',
          client_id: clientId,
          client_secret: clientSecret,
          username: this.login.email,
          password: this.login.password,
          scope: ''
        }
        const authUser = {}
        this.$http.post(loginUrl, postData)
          .then(response => {
            if (response.status === 200) {
              console.log('Oauth token', response)
              authUser.access_token = response.data.access_token
              authUser.refresh_token = response.data.refresh_token
              window.localStorage.setItem('authUser', JSON.stringify(authUser))
              this.$http.get(userUrl, {headers: getHeaders()})
                .then(response => {
                  console.log('user object', response)
                  authUser.email = response.body.email
                  authUser.name = response.body.name
                  window.localStorage.setItem('authUser', JSON.stringify(authUser))
                  this.$router.push({name: 'dashboard'})
                })
            }
            console.log(response)
          })
      }
    }
  }
</script>

<template>
  <div class="wrapper" id="home-wrapper">
    <section class="login">
      <div class="row">
        <div class="col-md-6 col-md-push-3">
          <div class="panel panel-default">
            <div class="panel-heading">
              <strong>Login</strong>
            </div>
            <div class="panel-body">
              <form @submit.prevent="handleLoginFormSubmit()">
                <div class="form-group">
                  <label>Email address</label>
                  <input class="form-control" placeholder="Enter your email address" type="text" v-model="login.email">
                </div>

                <div class="form-group">
                  <label>Password</label>
                  <input type="password" class="form-control" placeholder="Enter your password" v-model="login.password">
                </div>
                <button class="btn btn-primary">Login</button>
              </form>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<style lang="sass">
  @import 'assets/css/bootstrap.css';
  @import 'assets/css/app.css';
</style>

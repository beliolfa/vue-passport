<template>
  <form @submit.prevent="handleLogin">
      <label class="label" v-text="labels.user"></label>
      <p :class="controlClass">
        <input 
          :class="inputClass" 
          type="text" 
          :placeholder="userPlaceholder"
          v-model="login.user"
        >
      </p>
      <label class="label"  v-text="labels.password"></label>
      <p :class="controlClass">
        <input 
         :class="inputClass" 
         type="password" 
         :placeholder="passwordPlaceholder" 
         v-model="login.password"
        >
      </p>
      <p class="control">
        <button 
          type="submit" 
          class="button is-primary" 
          v-text="labels.button"
          :disabled="loading"
        ></button>
      </p>
  </form>
</template>

<script>
import axios from 'axios'

export default {
  name: 'VuePassportLogin',

  props: {
    labels: {
      type: Object,
      default () {
        return {
          title: 'Login',
          user: 'User',
          password: 'Password',
          button: 'Login'
        }
      }
    },

    userPlaceholder: String,

    passwordPlaceholder: String,

    controlClass: {
      type: String,
      default: 'control'
    },

    inputClass: {
      type: String,
      default: 'input'
    },

    apiUrl: {
      type: String,
      required: true
    },

    loginRoute: {
      type: String,
      default: 'oauth/token'
    },

    userRoute: {
      type: String,
      default: 'api/user'
    },

    clientId: {
      type: [Number, String],
      default: 2
    },

    secret: {
      type: String,
      required: true
    }
  },
  data () {
    return {
      loading: false,
      login: {
        user: '',
        password: ''
      }
    }
  },
  computed: {
    loginUrl () {
      return `${this.apiUrl}/${this.loginRoute}`
    },
    userUrl () {
      return `${this.apiUrl}/${this.userRoute}`
    }
  },
  methods: {
    getHeaders (token) {
      return {
        'Accept': 'application/json',
        'Authorization': `Bearer ${token}`
      }
    },
    handleLogin () {
      this.loading = true
      const postData = {
        grant_type: 'password',
        client_id: this.clientId,
        client_secret: this.secret,
        username: this.login.user,
        password: this.login.password,
        scope: ''
      }

      const authUser = {}

      axios.post(this.loginUrl, postData)
          .then(response => {
            authUser.access_token = response.data.access_token
            authUser.refresh_token = response.data.refresh_token

            const headers = this.getHeaders(authUser.access_token)

            axios.get(this.userUrl, {headers})
                .then(response => {
                  authUser.data = response.data
                  this.$emit('success', { authUser, headers })
                  this.loading = false
                })
                .catch(error => {
                  this.$emit('failed', error)
                  this.loading = false
                })
          })
          .catch(error => {
            this.$emit('failed', error)
            this.loading = false
          })
    }
  }
}
</script>

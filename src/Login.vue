<template>
  <section>
    <div :class="controlClass">
      <h1 v-text="labels.title"></h1>
    </div>

    <div :class="controlClass">
      <label class="label" v-text="labels.user"></label>
      <input
        :class="inputClass"
        type="text"
        :placeholder="userPlaceholder"
        v-model="login.user"
      >
    </div>

    <div :class="controlClass">
      <label class="label"  v-text="labels.password"></label>
      <input
       :class="[inputClass, {'reveal-password': passwordReveal}]"
       :type="typePassword"
       :placeholder="passwordPlaceholder"
       v-model="login.password"
      >
      <a v-if="passwordReveal && typePassword === 'password'" @click.prevent="toggleTypePassword">
        <i class="fa fa-eye fa-2x" aria-hidden="true"></i>
      </a>
      <a v-else-if="passwordReveal && typePassword === 'text'" @click.prevent="toggleTypePassword">
        <i class="fa fa-eye-slash fa-2x" aria-hidden="true"></i>
      </a>
    </div>

    <div :class="controlClass">
      <a
        @click.prevent="handleLogin"
        type="submit"
        :class="buttonClass"
        v-text="labels.button"
        :disabled="loading"
      ></a>
    </div>
  </section>
</template>

<script>
import axios from 'axios'

export default {
  name: 'VuePassportLogin',

  props: {
    labels: {
      type: Object,
      default() {
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

    passwordReveal: Boolean,

    controlClass: {
      type: String,
      default: 'control'
    },

    inputClass: {
      type: String,
      default: 'input'
    },

    buttonClass: {
      type: String,
      default: 'button'
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
  data() {
    return {
      loading: false,
      login: {
        user: '',
        password: ''
      },
      typePassword: 'password'
    }
  },
  computed: {
    loginUrl() {
      return `${this.apiUrl}/${this.loginRoute}`
    },
    userUrl() {
      return `${this.apiUrl}/${this.userRoute}`
    }
  },
  methods: {
    getHeaders(token) {
      return {
        Accept: 'application/json',
        Authorization: `Bearer ${token}`
      }
    },
    handleLogin() {
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

      axios
        .post(this.loginUrl, postData)
        .then(response => {
          authUser.access_token = response.data.access_token
          authUser.refresh_token = response.data.refresh_token

          const headers = this.getHeaders(authUser.access_token)

          axios
            .get(this.userUrl, { headers })
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
    },
    toggleTypePassword() {
      this.typePassword = this.typePassword === 'password' ? 'text' : 'password'
    }
  }
}
</script>

<style scoped>
.reveal-password {
  width: calc(100% - 40px);
  margin-right: 8px;
}
</style>

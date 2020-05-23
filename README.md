# vue-passport

A login component to handle authorization with Laravel Passport

## Install / Usage

```bash
$ npm install vue-passport
```

```bash
#ADD THESE ENV VARIABLES TO YOUR .env
VUE_APP_API_URL=https://your-api-url
VUE_APP_PASSPORT_SECRET=YOUR-PASSPORT-SECRET
```

```html
<template>
   <div id="app">
      <VuePassportLogin />
   </div>
</template>

<script>
import VuePassportLogin from 'vue-passport'

export default {
  components: { VuePassportLogin },
}
</script>
```

## Optional props

```bash
/*
 * Passport route that will get the access token
 */
loginRoute: {
  type: String,
  default: 'oauth/token'
},

/*
 * Personal API route that will get the user data
 */
userRoute: {
  type: String,
  default: 'api/user'
},

/*
 * Passport id from oauth_clients table
 */
clientId: {
  type: [Number, String],
  default: 2
},

/*
 * Login Form Labels
 */
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

/*
 * Placeholder for user input
 */
inputPlaceholder: String,

/*
 * Placeholder for password input
 */
passwordPlaceholder: String,

/*
 * Button for show/hidden password value
 * The icon is fontawesome.io by default
 */
passwordReveal: Boolean,

/*
 * Class for input wrapper
 */
controlClass: {
  type: String,
  default: 'control'
},

/*
 * Class for input
 */
inputClass: {
  type: String,
  default: 'input'
},

/*
 * Class for button
 */
buttonClass: {
  type: String,
  default: 'button'
},
```

## Events

You can use `success` event for trigger your local method when login was successfuly made.

It returns a payload with the user and the headers

You can use `failed` event for trigger your local method when something was wrong.

```html
<template>
   <div id="app">
      <VuePassportLogin
        @success="handleLogin"
        @failed="handleErrors"
      />
   </div>
</template>

<script>
import VuePassportLogin from 'vue-passport'
import axios from 'axios'

var instance = axios.create();

export default {
  components: { Login },
  methods: {
    handleLogin (payload) {
      this.$store.dispatch('setUser', payload.authUser)
      instance.defaults.headers.common['Authorization'] = payload.headers.Authorization;
    },
    handleErrors (errors) {
      alert('Authorization error' + errors)
    }
  }
}
</script>
```

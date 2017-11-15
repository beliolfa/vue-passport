# vue-passport

A login component to handle authorization with Laravel Passport

## Install / Usage
``` bash
$ npm install vue-passport
```

```html
<template>
   <div id="app">
      <VuePassportLogin 
        :api-url="apiUrl"
        :secret="secret"
      />
   </div>
</template>

<script>
import VuePassportLogin from 'vue-passport'

export default {
  components: { VuePassportLogin },

  data () {
    return {
      apiUrl: process.env.API_URL,
      secret: process.env.PASSPORT_SECRET
    }
  },
}
</script>
```

## Optional props
``` bash
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
```
## Events

You can use `success` event for trigger your local method when login was successfuly made.

It returns a payload with the user and the headers

You can use `failed` event for trigger your local method when something was wrong.


```html
<template>
   <div id="app">
      <VuePassportLogin 
        :api-url="apiUrl"
        :secret="secret"
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
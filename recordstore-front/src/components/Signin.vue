<template>
  <div class="max-w-sm m-auto my-8">
    <div class="border p-10 border-gray-400 shadow rounded">
      <h3 class="text-2xl mb-6 text-gray-light-darkest">Sign In</h3>
      <form @submit.prevent="signin">
        <div class="text-red" v-if="error">{{ error }}</div>
        <div class="mb-6">
          <label for="email" class="label">Email Address</label>
          <input type="email" v-model="email" class="input w-auto" id="email" placeholder="email@email.com">
        </div>

        <div class="mb-6">
          <label for="password" class="label">Password</label>
          <input type="password" v-model="password" class="input" id="password">
        </div>

        <button type="submit" class="font-sans font-bold px-4 rounded cursor-pointer no-underline bg-green-600 hover:bg-green-900 block w-full py-4 text-white items-center justify-center">Sign In</button>

        <div class="my-4">
          <router-link to="/signup" class="link underline">Sign Up</router-link>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Signin',
  data () {
    return {
      email: '',
      password: '',
      error: ''
    }
  },
  created () {
    this.checkSignedIn()
  },
  updated () {
    this.checkSignedIn()
  },
  methods: {
    signin () {
      this.$http.plain.post('/signin', {email: this.email, password: this.password})
        .then(response => this.signinSuccessful(response))
        .catch(error => this.signinFailed(error))
    },
    signinSuccessful (response) {
      if (!response.data.csrf) {
        this.signinFailed(response)
        return
      }
      localStorage.csrf = response.data.csrf
      localStorage.signedIn = true
      this.error = ''
      this.$router.replace('/records')
    },
    signinFailed (error) {
      this.error = (error.response && error.response.data && error.response.data.error) || ''
      delete localStorage.csrf
      delete localStorage.signedIn
    },
    checkSignedIn () {
      if (localStorage.signedIn) {
        this.$router.replace('records')
      }
    }
  }
}
</script>

<template>
  <div>
    <label class="form-label">Email:</label>
    <input class="form-control" type="text" v-model="email" @keyup.enter="login"/>
    <label class="form-label">Password</label>
    <input class="form-control" type="password" v-model="password" @keyup.enter="login"/>
    <button class="btn btn-lg btn-primary mt-5 w-100" @click="login" @keyup.enter="login">Login</button>
  </div>
</template>

<script>

export default {
  name: 'login',
  props: {
    baseurl: String
  },
  data(){
    return{
      email:"",
      password:""
    }
  },
  methods:{
    login(){
      this.$emit('login',{email: this.email, password: this.password})
    }
  },

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>

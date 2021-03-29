<template>
    <div class="bg-primary p-1"><h2 class="text-light">Navitag Logistics</h2></div>
    <div class="container my-5">
      <login v-if="tab == 'login'" :baseurl="baseurl" @login="login"/>
      <addressInput v-if="tab == 'book'" :baseurl="baseurl" @book="createshipment" :client="booker" @error="msgopen"></addressInput>
    </div>
    <modal v-if="loading" class="text-center">
      <div class="spinner-border m-5" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
    </modal>
    <modal v-if="dismissmsg" class="text-center">
      <div class="p-3">
        <h3>{{dismissmsg}}</h3>
        <button class="btn btn-secondary mt-3" @click="msgdismiss">OK</button>
      </div>
    </modal>

</template>

<script>

import 'bootstrap'
import 'bootstrap/dist/css/bootstrap.min.css'
import addressInput from '@/components/addressInput.vue'
import login from '@/components/login.vue'
import modal from '@/components/modal.vue'
import axios from 'axios'

export default {
  name: 'App',
  components: {
    addressInput, login, modal
  },
  data(){
    return {
      tab : "login",
      baseurl: "https://james.local/jbx/backend/",
      //baseurl: "https://shiplabel.navitag.net/vercel-test/",
      booker: null,
      loading: true,
      dismissmsg: false
    }
  },
  mounted(){
    if(sessionStorage.getItem("navitagBooker") != null && sessionStorage.getItem("navitagBooker") != ""){
      this.login({id: sessionStorage.getItem("navitagBooker")})
    } else{
      this.loading = false
    }
  },
  methods:{
    login(body){
      this.loading = true
      let comp = this
      let error = false
      axios.post(this.baseurl + 'accountlogin.php', body
      ).then(function(res){
        if(res.data.status == "success" && res.data.response.length > 0) {
          console.log(res.data.response[0].id)
          sessionStorage.setItem("navitagBooker", res.data.response[0].id)
          comp.booker = res.data.response[0]
          comp.tab = "book"
        } else{
          error = "Invalid Login"
        }
      }).catch(function(er){
        console.log(er)
      }).then(function(){
        comp.loading = false
        comp.dismissmsg = error
      })
    },
    msgopen(msg){
      this.dismissmsg = msg
    },
    msgdismiss(){
      this.dismissmsg = false
    },
    createshipment(data){
      console.log(data)
    }
  }
}
</script>

<style>

</style>

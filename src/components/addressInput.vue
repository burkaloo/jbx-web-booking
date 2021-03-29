<template>
  <div>
    <h2>Book Shipment</h2>
    <label class="form-label mt-2">Name:</label>
    <input class="form-control" type="text" v-model.trim="name"/>
    <label class="form-label mt-2">Company</label>
    <input class="form-control" type="text" v-model.trim="company"/>
    <label class="form-label mt-2">Cell Number</label>
    <input class="form-control" type="text" maxlength="11" v-model.trim="contact"/>
    <label class="form-label mt-2">Unit/Floor #</label>
    <input class="form-control" type="text" v-model.trim="unitflr"/>
    <label class="form-label mt-2">Building Name</label>
    <input class="form-control" type="text" v-model.trim="bldg"/>
    <label class="form-label mt-2">Street Address</label>
    <input class="form-control" type="text" v-model.trim="street"/>
    <label class="form-label mt-2">Subdivision</label>
    <input class="form-control" type="text" v-model.trim="subdiv"/>
    <label class="form-label mt-2">Barangay</label>
    <input class="form-control" type="text" v-model.trim="brgy"/>
    <label class="form-label mt-2">City</label>
    <select class="form-select" v-model="city">
      <option v-for="(opt, index) in cityOpts" :value="opt" :key="index">{{opt}}</option>
    </select>
    <label class="form-label mt-2">Zip Code</label>
    <input class="form-control" type="text" v-model.trim="zip"/>
    <label class="form-label mt-2">Province:</label>
    <input class="form-control" type="text" v-model="province" readonly/>
    <label class="form-label mt-2">COD Amt:</label>
    <input class="form-control" type="text" v-model.trim="cod"/>
    <label class="form-label mt-2">Serivce:</label>
    <select class="form-select" v-model="service">
      <option v-for="(opt, index) in serviceOpts" :value="opt" :key="index">{{opt.text}}</option>
    </select>
    <button class="w-100 btn btn-lg btn-primary mt-3" @click="book">Book Shipment</button>
  </div>
  <modal v-if="status != 'none'" class="text-center">
    <div class="p-3">
      <div v-if="status == 'generating'">
        <p>Generating Label...</p>
        <div class="spinner-border m-5" role="status">
          <span class="visually-hidden">Loading...</span>
        </div>
      </div>
      <div v-if="status == 'error'">
        <h4>Network Error, please try again</h4>
        <button class="btn btn-secondary mt-3" @click="close">OK</button>
      </div>
      <div v-if="status == 'success'">
        <h4>tracking: {{trackingnum}}</h4>
        <a :href="trackinglink" target="_blank" class="w-50"><button class="btn btn-secondary mt-3">Open in tab</button></a><button class="btn btn-danger mt-3 w-50" @click="closeClear">close</button>
      </div>
    </div>
  </modal>
</template>

<script>
import axios from 'axios'
import modal from '@/components/modal.vue'

export default {
  name: 'addressInput',
  props: {
    baseurl:String,
    client:{}
  },
  components:{
    modal
  },
  data(){
    return{
      name:"",
      contact:"",
      unitflr: "",
      bldg:"",
      street:"",
      subdiv: "",
      brgy:"",
      city:null,
      cityOpts:[
        "Caloocan",
        "Las Piñas",
        "Makati",
        "Malabon",
        "Mandaluyong",
        "Manila",
        "Marikina",
        "Muntinlupa",
        "Navotas",
        "Parañaque",
        "Pasay",
        "Pasig",
        "Pateros",
        "Quezon City",
        "San Juan",
        "Taguig",
        "Valenzuela"
      ],
      province:"Metro Manila",
      service: {text:"Metro Manila Small Pouch", price:80},
      serviceOpts:[
        {text:"Metro Manila Small Pouch", price:80},
      ],
      cod:"",
      zip:"",
      company:"",
      status: 'none',
      trackingnum:'',
      trackinglink:'',
    }
  },
  methods:{
    clear(){
      this.name = ""
      this.contact = ""
      this.unitflr = ""
      this.bldg = ""
      this.street= ""
      this.subdiv = ""
      this.brgy = ""
      this.city = null
      this.zip = ""
      this.trackingnum = ''
      this.trackinglink = ''
      this.cod = ""
    },
    book(){
      var notValid = this.validate()

      if(notValid){
        this.$emit('error', notValid)
      } else{
        let comp =  this
        this.status = "generating"
        var details = {
          name: this.name,
          contact: this.contact,
          unit: this.unitflr,
          bldg: this.bldg,
          street: this.street,
          subdivision: this.subdiv,
          brgy: this.brgy,
          city: this.city,
          prov: this.province,
          service: this.service,
          zip: this.zip,
          cod: isNaN(parseInt(this.cod)) ? "" : parseInt(this.cod),
          company: this.company,
          bookerid: this.client.id,
          bookercompany: this.client.company
        }
        axios.post(this.baseurl + "createbooking.php", details
         ).then(function(res){
           if(res.data.status == 'success'){
             comp.trackingnum = res.data.response.tracking
             comp.trackinglink = res.data.response.label
           }
           comp.status = res.data.status
        }).catch(function(err){
            console.log(err)
            comp.status = 'error'
        })
        this.$emit('book', details)
      }
    },
    validate(){
      var error = false
      if(this.name == ""){
        error = "Name required"
      } else if(this.contact == ""){
        error = "Contact Number Required"
      } else if(this.contact.length < 11 || isNaN(this.contact)){
        error = "Contact Numner Invalid"
      } else if(this.bldg == "" && this.steet == ""){
        error = "Must have building and/or street addressInput"
      } else if(this.brgy == ""){
        error = "Barangay Required"
      } else if(this.city == null){
        error = "Please selet city"
      } else if(this.cod != "" && isNaN(parseInt(this.cod)) ){
        error = "Invalid COD amount"
      } else if(this.zip == ""){
        error = "Zip code required"
      } else{
        error = false
      }
      return error
    },
    close(){
      this.status = 'none'
    },
    closeClear(){
      this.status = 'none'
      this.clear()
    }

  },

}
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>

<template>
  <div>
    <h1>invoice create page</h1>
    <p>เลือก customer</p>
    <b-form-select v-model="customer_select" signle-line label="Filter">
      <b-form-select-option value="">Please select a customer</b-form-select-option>
      <b-form-select-option v-for="customer in customers" :key="customer.customer_id" :value="customer.customer_id">{{customer.customer_name}}</b-form-select-option>
    </b-form-select>
    <b-button variant="outline-primary" @click="create()">create</b-button>
  </div>
</template>

<script>
export default {
  mindleware:'auth',
  data(){
    return{
      sellers:[],
      customers:[],
      customer_select:'',
    }
  },
  created(){

    this.getData()
  },
  methods:{
    async getData(){
      let seller = await this.$http.get(`/sellers?user_id=${this.$auth.user.id}`)
          this.sellers = seller.data.data
          console.log("data sellers :" ,this.sellers[0].seller_id)


      let customer = await this.$http.get('/customers')
          this.customers = customer.data.data
          console.log("data customers:" ,this.customers)
    },
    async create(){
      let invoice = await this.$http.post('invoice/create',{
        seller_id:this.sellers[0].seller_id,
        customer_id:this.customer_select
      }).then(value=>{
        if(value){
          console.log(value.data.invoice.invoiceNo)
          this.$router.push({path:'addDetail',query:{invoiceNo:value.data.invoice.invoiceNo,invoiceId:value.data.invoice.id}})
        }
      })
    }
  }


}
</script>

<style>

</style>

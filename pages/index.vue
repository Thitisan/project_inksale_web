<template>
  <div>
    <h1>detail</h1>
    <ul>
      <li v-for="billlist in billlists" :key="billlist.invoicenumber_id">
        <b-link @click="viewList(billlist.invoiceNo)">{{ billlist.invoiceNo }}</b-link>
        <p>customer: {{billlist.customer_name}}</p>
        <p>seller: {{billlist.seller_name}}</p>
        <p>sum price: {{billlist.sum_price}}</p>
      </li>
    </ul>

    <b-button variant="outline-primary" @click="addinvocie()">New Invoice</b-button>

  </div>
</template>

<script>
export default {
  middlewere:'auth',
  components: {

  },
  data(){
    return{
      sellers:[],
      seller_select:'',
      customers:[],
      customer_select:'',
      inks:[],
      bills:[],
      billlists:[],
      act:[],
    }
  },
  async created() {
      this.act = await this.$auth.user
      console.log('user',this.act)
      console.log('created')
      this.getData()

    },
  methods:{
    async getData() {

          let seller = await this.$http.get(`/sellers?user_id=${this.$auth.user.id}`)
          this.sellers = seller.data.data
          console.log("data sellers :" ,this.sellers[0].seller_id)


          let customer = await this.$http.get('/customers')
          this.customers = customer.data.data
          console.log("data customers:" ,this.customers)

          if(this.act.roles === 'admin'){
            let billlist = await this.$http.get(`/invoice`)
            this.billlists = billlist.data.data
            console.log("data billlist:" ,this.billlists)
          }else{
            let billlist = await this.$http.get(`/invoice?seller_id=${this.sellers[0].seller_id}`)
            this.billlists = billlist.data.data
            console.log("data billlist:" ,this.billlists)
          }


      },

      addinvocie(){
        this.$router.push({path:'invoice/create'})
      },

      viewList(id){
        this.$router.push({path:'invoice/editDetail',query:{id:id}})
      }

  }
}
</script>

<style>

</style>

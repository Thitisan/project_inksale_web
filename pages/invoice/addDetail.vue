<template>
  <div>
    <caption>Invoice NO: {{IVN}}</caption>
    <div>
      <b-table-simple>
        <b-thead head-variant="dark">
          <b-tr>
            <b-th>index</b-th>
            <b-th>ink</b-th>
            <b-th>price</b-th>
            <b-th>amount</b-th>
            <b-th>sum price</b-th>
            <b-th>action</b-th>
          </b-tr>
        </b-thead>
        <b-tbody v-for="(billlist,index) in billlists" :key="billlist.index">
          <b-tr>
            <b-th>{{index+1}}</b-th>
            <b-th>
              <b-form-select v-model="billlist.ink_id" signle-line label="Filter">
              <b-form-select-option value="">Please select a ink</b-form-select-option>
              <b-form-select-option v-for="ink in inks" :key="ink.ink_id" :value="ink.ink_id">{{ink.ink_name}}</b-form-select-option>
              </b-form-select>
            </b-th>
            <b-th>
              {{billlist.ink_price}}
            </b-th>
            <b-th>
              <b-form-input  v-model="billlist.amount"></b-form-input>
            </b-th>
            <b-th>
              {{billlist.ink_price*billlist.amount}}
            </b-th>
            <b-th>
              <b-button variant="outline-primary" @click="edit()">Edit</b-button>
              <b-button variant="outline-primary" @click="Delete()">Delete</b-button>
            </b-th>
          </b-tr>
        </b-tbody>
        <b-tfoot>
          <b-tr>
            <b-td colspan="7" variant="secondary" class="text-right">
              Total price: <b>{{sumprice}}</b>
            </b-td>
          </b-tr>
          <b-tr>
            <b-td>
              <b-button variant="outline-primary" @click="addtable()">+</b-button>
              <b-button variant="outline-primary" @click="distable()">-</b-button>
            </b-td>
          </b-tr>
        </b-tfoot>
      </b-table-simple>
    </div>

    <b-button variant="outline-primary" @click="addbill()">Add bill</b-button>
  </div>
</template>

<script>
export default {
  middleware:'auth',
  data(){
    return {
      sellers:[],
      seller_select:'',
      customers:[],
      customer_select:'',
      inks:[],
      ink_select:[],
      ink_amount:'',
      IVN:'',
      countTables:1,
      billlists:[],
    }
  },
  created(){
    this.getData()

  },
  computed:{
  sumprice:function(){
    var sum =0
    for(const billlist of this.billlists){
      console.log('loop sum test',billlist.amount*billlist.ink_price)
      sum = (billlist.amount*billlist.ink_price)+sum
    }
    return sum
  }

  },
  methods:{
    async getData() {

          this.IVN = this.$route.query.invoiceNo
          let seller = await this.$http.get('/sellers')
          this.sellers = seller.data.data
          console.log("data sellers :" ,this.sellers)

          let ink = await this.$http.get('/inks')
          this.inks = ink.data.data
          console.log("data inks:" ,this.inks)

          let customer = await this.$http.get('/customers')
          this.customers = customer.data.data
          console.log("data customers:" ,this.customers)

      },
    async addbill(){

      for(const billlist of this.billlists){
        // console.log('loop',billlist.ink.ink_name)
          await this.$http.post('/bills/create', {
                invoicenumber_id:this.$route.query.invoiceId,
                ink_id:billlist.ink_id,
                amount:billlist.amount,
            })
      }

      this.$router.push({path:'.'})
      // this.billlists.forEach(element => {
      //   console.log("show list",element.ink.ink_name)
      //   console.log("show query",this.$route.query.invoiceId)


      // });

    },
    addtable(){
      this.billlists.push({id:"",ink:"",amount:''})


    },
    distable(){
      this.billlists.pop()
    }
  }
}
</script>

<style>

</style>

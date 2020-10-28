<template>
  <div>
    <caption>Invoice NO: {{invoiceNo}}</caption>
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
              <b-form-select v-model="billlist.ink" signle-line label="Filter">
              <b-form-select-option value="">Please select a ink</b-form-select-option>
              <b-form-select-option v-for="ink in inks" :key="ink.ink_id" :value="{ink_id:ink.ink_id,ink_price:ink.ink_price}">{{ink.ink_name}}</b-form-select-option>
              </b-form-select>
            </b-th>
            <b-th>
              {{billlist.ink.ink_price}}
            </b-th>
            <b-th>
              <b-form-input  v-model="billlist.amount"></b-form-input>
            </b-th>
            <b-th>
              {{billlist.ink.ink_price*billlist.amount}}
            </b-th>
            <b-th>

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
      billdetail:[],
      sumprices:'',
      seller_id:'',
      customer_id:'',
      invoiceNo:'',
      invoice_id:'',
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
      sum = (billlist.amount*billlist.ink.ink_price)+sum
    }
    return sum
  }

  },
  methods:{
    async getData() {

          let billdetail = await this.$http.get(`/invoice?invoiceNo=${this.$route.query.invoiceNo}`)
          this.billdetail = billdetail.data.data
          this.seller_id = billdetail.data.data[0].seller_id
          this.customer_id = billdetail.data.data[0].customeid
          this.invoiceNo = billdetail.data.data[0].invoiceNo
          this.invoice_id = billdetail.data.data[0].invoicenumber_id
          console.log("data billdetail:" ,this.billdetail)

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
                ink_id:billlist.ink.ink_id,
                amount:billlist.amount,
            })
      }
      await this.$http.put(`/invoice/update/${this.$route.query.invoiceId}`, {
                invoicenumber_id:this.$route.query.invoiceId,
                seller_id:this.seller_id,
                customer_id:this.customer_id,
                sum_price:this.sumprice
            })

      this.$router.push({path:'/'})


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

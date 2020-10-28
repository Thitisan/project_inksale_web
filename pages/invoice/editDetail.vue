<template>
  <div>
    <p>seller:{{seller_name}}</p>
    <p>customer:{{customer_name}}</p>
    <p>invoice number:{{invoiceNo}}</p>
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
      inks:[],
      ink_select:[],
      ink_amount:'',
      seller_name:'',
      customer_name:'',
      invoiceNo:'',
      invoice_id:'',
      billdetail:[],
      billlists:[],
      sum_price:''
    }
  },
  created(){
    this.getData()
    console.log(this.$route.query.id)


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

          let billdetail = await this.$http.get(`/invoice?invoiceNo=${this.$route.query.id}`)
          this.billdetail = billdetail.data.data
          this.seller_name = billdetail.data.data[0].seller_name
          this.customer_name = billdetail.data.data[0].customer_name
          this.invoiceNo = billdetail.data.data[0].invoiceNo
          this.invoice_id = billdetail.data.data[0].invoicenumber_id
          console.log("data billdetail:" ,this.invoice_id)


          let ink = await this.$http.get('/inks')
          this.inks = ink.data.data
          console.log("data inks:" ,this.inks)

          let res = await this.$http.get(`/bills?invoiceNo=${this.$route.query.id}`)
          this.billlists = res.data.data
          console.log('new data:',this.billlists)



      },
    async addbill(){
      this.$http.delete(`/bills/${this.invoice_id}/delete`)
      for(const billlist of this.billlists){
        console.log('loop',billlist.ink_id)
          await this.$http.post('/bills/create', {
                invoicenumber_id:this.invoice_id,
                ink_id:billlist.ink_id,
                amount:billlist.amount,
            })
      }

      this.$router.push({path:'.'})


    },
    addtable(){
      this.billlists.push({id:"",ink:"",amount:''})
    },
    distable(){
      this.billlists.pop()
    },
    sumprice(){
      for(const billlist of this.billlists){
      console.log('test sum',billlist)
    }
    }
  }
}
</script>

<style>

</style>

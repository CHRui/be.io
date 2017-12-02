<template>
    <div class="tmpl">
        {{orderid}}
        <input type="text" v-model="amount">

        <el-button type="success" size="small" @click="pay">支付</el-button>
    </div>
</template>

<script>
  export default{
    data(){
      return{
        orderid:this.$route.params.orderid,
        amount:0
      }
    },
    mounted(){
      this.getorderInfo();
    },
    methods:{
      getorderInfo(){
        this.$ajax.get('/site/validate/order/getorderdetial/'+this.orderid).then(res=>{
          this.amount = res.data.message.ordeerinfo.order_amount;
        });
      },
      pay(){
        var url = '/site/validate/order/pay/'+this.orderid;
        this.$ajax.get(url).then(res=>{
          if(res.data.status == 1){
            this.$message.error(res.data.message);
            return;
          }
          this.$router.push({name:'paysuccess'});
        });
      }
    }
  }
</script>
<style scoped>
  
</style>
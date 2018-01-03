<template>
 <div id="app">
   <Header :seller="seller"></Header>
   <div class="tab">
     <router-link to="/goods" class="tab-item" tag="div">商品</router-link>
     <router-link to="/ratings" class="tab-item" tag="div">评价</router-link>
     <router-link to="/seller" class="tab-item" tag="div">商家</router-link>
   </div>
   <router-view></router-view>
 </div>
</template>

<script>
 import Header from './components/header/header.vue';
 const ERR_OK = 0;
 export default{
   data() {
     return {
       seller:{}
     }
   },
   created () {
     this.$http.get('/api/seller')
     .then((response) => {
       response = response.body
       if(response.errno === ERR_OK){
         this.seller = response.data
       }
     })
   },
   components: {
     Header
   }
 };
</script>

<style  lang="stylus"  scoped rel="stylesheet/stylus">
#app
   .tab
      display flex
      width 100%
      height 40px
      line-height 40px
      .tab-item
        flex 1
        text-align center 
</style>

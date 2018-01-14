<template>
 <div id="app">
   <Header :seller="seller"></Header>
   <div class="tab">
     <router-link to="/goods" class="tab-item" tag="div" >商品</router-link>
     <router-link to="/ratings" class="tab-item" tag="div">评价</router-link>
     <router-link to="/seller" class="tab-item" tag="div">商家</router-link>
   </div>
    <keep-alive>
        <router-view :seller="seller"></router-view>
    </keep-alive>
    <!-- keep-alive --> <!-- 保留组件状态使路由切换的时候组件不会从新加载-->  
 </div>
</template>

<script>
 import Header from './components/header/header.vue';
 import BScroll from 'better-scroll';
 import {urlParse} from './common/js/util'
 const ERR_OK = 0;
 export default{
   data() {
      return {
        seller: {
          id: (() => {
            let queryParam = urlParse();
            return queryParam.id;
          })()
        }
      };
    },
   created () {
     this.$http.get('/api/seller?id='+this.seller.id)
     .then((response) => {
       response = response.body
       if(response.errno === ERR_OK){
          this.seller = Object.assign({}, this.seller, response.data);
          console.log(this.seller)
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
        &.active
           color: rgb(240, 20, 20)
</style>

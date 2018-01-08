<template>
 <div class="cartcontrol">
    <transition name="move">
     <div class="cart-decrease"
      v-show="food.count>0"
       @click.stop.prevent="decreaseCart">
       <span class="icon-remove_circle_outline inner"></span>
     </div>
    </transition> 
     <div class="cart-count " v-show="food.count>0">{{ food.count }}</div>
     <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
 </div>
</template>

<script>
 import Vue from 'vue';
 export default {
   props: {
       food: {
           type: Object
       }
   },  
   methods: {
       addCart() {
           if(!event._constructed){
               return
           }
           if(!this.food.count) {
               //    添加数据
               Vue.set(this.food,'count',1) ;     
           }else{
               this.food.count++;
           }
              //    向父级派发事件
           this.$emit('add',event.target)
       },
       decreaseCart() {
           if(!event._constructed){
               return
           }
           if(this.food.count){
               this.food.count--;
           }
       }
   },
   data() {
     return {

     }

   },
   components: {

   }
 }
</script>

<style scoped lang="stylus">
  .cartcontrol
     font-size 0 
     .cart-decrease
        display inline-block
        padding 6px
        .inner
          display inline-block
          font-size 24px
          line-height 24px
          color  rgb(0,160,220)
          transition all 0.4s linear
          transform rotate(0)
        &.move-enter-active,&.move-leave-active
          transition: all 0.4s linear
        &.move-enter,&.move-leave-to
          opacity: 0
          transform: translate3d(24px, 0, 0)  
          .inner
              transform rotate(180deg)
      .cart-count
        display inline-block
        vertical-align top
        width 12px
        padding-top 6px
        line-height 24px
        text-align center
        font-size 10px
        color rgb(147,153,159)
      .cart-add
        display inline-block
        font-size 24px
        line-height 24px
        padding 6px
        color  rgb(0,160,220)
</style>

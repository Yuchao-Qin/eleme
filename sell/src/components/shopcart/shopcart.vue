<template>
 <div class="shopcart">
     <div class="content">
         <div class="content-left">
             <div class="logo-wrapper">
                 <div class="logo" :class="{'highlight':totalCount>0}">
                     <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
                 </div>
                 <div class="num" v-show="totalCount>0">{{ totalCount }}</div>
             </div>
             <div class="price" :class="{'heighlight':totalPrice>0}">￥{{ totalPrice }}</div>
             <div class="desc">另需配送费￥{{deliveryprice}}元</div>
         </div>
         <div class="content-right">
             <div class="pay" :class="payClass">
                 {{ payDesc }}
             </div>
         </div>
         <div class="ball-container">
             <transition name="drop">
                <div v-for="(ball,index) in balls" v-show="ball.show" :key="index" class="ball"></div>
                <div class="innerr"></div>        
             </transition>       
         </div>
     </div>
 </div>
</template>

<script>
 export default {
   props:{
    selectFoods:{
        type:Array,
        default() {
            return [
                {
                    price:10,
                    count:2
                }
            ];
        }
    },   
    deliveryprice:{
        type:Number,
        defaule: 0
    },
    minprice:{
        type:Number,
        defaule: 0
    },
   }  ,
   data() {
     return {
         balls:[
             {
                 show:false
             },
             {
                 show:false
             },
             {
                 show:false
             },
             {
                 show:false
             },
             {
                 show:false
             },
         ]
     }
   },
   computed: {
    totalPrice() {
        let total = 0;
        this.selectFoods.forEach((item) => {
            total += item.price * item.count;
            })
            return total;
    },
    totalCount() {
        let count = 0;
        this.selectFoods.forEach((item)=>{
            count += item.count;
        });
        return count
    },
    payDesc() {
        if(this.totalPrioce === 0){
            return `￥$(this.mimprice)元起送`;
        }else if (this.totalPrice<this.minprice){
            let diff = this.minprice - this.totalPrice
            return `还差${diff}元起送`
        }else{
            return '去结算'
        }
    },
    payClass() {
        if(this.totalPrice<this.minprice){
            return 'not-enough';
        }else {
            return 'enough'
        }
    }
   }
 }
</script>

<style scoped lang="stylus">
    .shopcart
      position fixed
      left 0
      bottom 0
      z-index 50
      width 100%
      height 48px
      background-color #000  
      .content
        display flex
        background-color #141d27
        .content-left
           flex 1
           font-size 0
           .logo-wrapper
             display inline-block  
             position relative
             top -10px
             margin 0 12px
             padding 6px
             width 56px
             height 56px
             box-sizing border-box
             vertical-align top
             border-radius 50% 
             background-color #141d27
             .logo
               width 100%
               height 100%
               border-radius 50%
               text-align center
               background-color #243b3c  
               &.highlight
                  background-color rgb(0,160,220)
               .icon-shopping_cart
                   font-size 24px
                   color #80858a
                   line-height 44px
                   &.highlight
                      color #fff
           .num  
             position absolute
             top 0
             right 0
             width 24px
             height 16px      
             line-height 16px
             text-align center
             border-radius 16px
             font-weight 700
             color #ffffff
             font-size 9px
             background-color rgb(240,20,20)
             box-shadow 0 4px 8px 0 rgba(0,0,0,0.4)

           .price
             display inline-block
             vertical-align top
             margin-top 12px
             vertical-align top
             line-height 24px
             box-sizing border-box
             padding-right 12px
             border-right 1px solid rgba(255,255,255,0.1)
             font-size 16px
             font-weight 700
             color rgba(255,255,255,0.4)
             &.heighlight
               color #ffffff
           .desc    
             display inline-block
             vertical-align top
             line-height 24px
             margin 12px 0 0 12px
             color rgba(255,255,255,0.4)
             font-size 10px
       
        .content-right
            flex 0,0,105px
            width 105px      
            .pay
              height 48px
              line-height 48px
              text-align center
              font-size 12px  
              color rgba(255,255,255,0.4)
              font-weight 700
              background-color #2b333b
              &.not-enough
                 background-color #2b333b
              &.enough
                 background-color #00b43c
                 color #fff
      .ball-container
         .ball
           position fixed
           left 32px
           bottom 22px
           z-index 200
           &.drop-enter-active,&.drop-leave-active
               transition all 0.4s
               .inner  
                 width 16px
                 height 16px
                 border-radius 50%
                 background-color rgb(0,160,220)  

                 
                 
</style>

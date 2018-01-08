<template>
<div>
  <div class="shopcart">
     <div class="content" @click="toggleList">
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
         <div class="content-right" @click.stop.prevent="pay">
             <div class="pay" :class="payClass">
                 {{ payDesc }}
             </div>
         </div>
         <div class="ball-container">
           <div v-for="(ball, index) in balls" :key=index>
             <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
                <div v-show="ball.show" class="ball">
                    <div class="inner inner-hook"></div>  
                </div>      
             </transition>       
            </div>
         </div>
        <transition name="fold">  
         <div class="shopcart-list"  v-show="listShow">
            <div class="list-header">
                <h1 class="title">购物车</h1>
                <span class="empty" @click="empty">清空</span>
            </div>
            <div class="list-content" ref="listContent">
              <ul>  
                <li class="food" v-for="(food,index) in selectFoods" :key="index">                    
                    <span class="name">{{ food.name }}</span>
                    <div class="price">
                        <span>￥{{ food.price*food.count }}</span>
                    </div>
                    <div class="cartcontrol-wrapper">
                        <cartcontrol :food="food"></cartcontrol>
                    </div>
                </li>
              </ul>  
            </div>         
         </div> 
        </transition>
     </div>
  </div>
  <transition name="fade">    
    <div class="list-mask" @click="hideList" v-show="listShow"></div>
  </transition>
</div> 
</template>

<script>
import cartcontrol from '../cartcontrol/cartcontrol';
import BScroll from 'better-scroll';
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
                 show:false,
             },
             {
                 show:false,
             },
             {
                 show:false,
             },
             {
                 show:false,
             },
             {
                 show:false,
             },
         ],
         dropBalls:[],
         fold:true   //  折叠状态为true
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
    },
    listShow() {
        if (!this.totalCount) {
            this.fold = true;
            return false
        }
        let show = !this.fold;
        if (show) {
            this.$nextTick(()=>{
               if(!this.scroll) {
                  this.scroll = new BScroll(this.$refs.listContent,{
                   click:true
                 });  
               } else {
                   this.scroll.refresh();
                //    不用从新实例化 
               }
            })
        }
        return show;
    }
   },
   methods: {
       drop(el){
           console.log(el);
           for (let i=0;i<this.balls.length;i++){
               let ball = this.balls[i];
               if (!ball.show){
                   ball.show = true;
                   ball.el = el;
                   this.dropBalls.push(ball);
                   return;
               }
           }
       },
       empty() {
        //    清空
           this.selectFoods.forEach((food)=>{
               food.count = 0;
           })
       },
       pay() {
           if (this.totalPrice < this.minprice){
               return
           }
           window.alert(`支付${this.totalPrice}元`)
       },
       beforeDrop(el) {
          console.log(el);
          let count = this.balls.length;
          while(count--) {
              let ball = this.balls[count];
              if (ball.show) {
                  let rect =ball.el.getBoundingClientRect();
                  let x = rect.left - 32;
                  let y = -(window.innerHeight - rect.top - 22);
                  el.style.display = '';
                  el.style.webkitTransform = `translate3d(0,${y}px,0)`;
                  el.style.transform = `translate3d(0,${y}px,0)`;
                  let inner = el.getElementsByClassName('inner-hook')[0];
                  inner.style.webkitTransform = `translate3d(${x}px,0,0)`
                  inner.style.transform = `translate3d(${x}px,0,0)`            
              }
          } 
       },
       dropping(el,done) {
           let rf = el.offsetHeight;
           //触发浏览器重绘
               this.$nextTick(()=>{
                   el.style.webkitTransform = 'translate3d(0,0,0)';
                   el.style.transform = 'translate3d(0,0,0)';
                   let inner = el.getElementsByClassName('inner-hook')[0];
                   inner.style.webkitTransform = 'translate3d(0,0,0)';
                   inner.style.transform = 'translate3d(0,0,0)';
                   el.addEventListener('transitionend',done);
                //   过渡效果已经完成。。。。。。???
               });
       },
       afterDrop(el) {
           let ball = this.dropBalls.shift();
           if(ball) {
               ball.show = false;
               el.style.display = 'none';
           }
       },
       toggleList() {
       if(!this.totalCount) {
           return
        }
        this.fold = !this.fold
        },
        hideList() {
            this.fold = true;
            // 触发listShow重新计算
        }
   },
   components: {
       cartcontrol
   }
 }
</script>

<style scoped lang="stylus">
    @import "../../common/stylus/mixin.styl";
    .list-mask
      position fixed
      top 0
      left 0
      width 100%
      height 100%
      z-index 40
      backdrop-filter:blur(10px)
      opacity 1
      background-color rgba(7,17,27,0.6)
      &.fade-enter-active,&.fade-leave-active
         transition: all 0.5s
      &.fade-enter,&.fade-leave-active
         opacity 0
         background-color rgba(7,17,27,0)   
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
           transition all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
           .inner  
               width 16px
               height 16px
               border-radius 50%
               background-color rgb(0,160,220)  
               transition: all 0.4s linear
      .shopcart-list
         position absolute    
         background-color #fff
         left 0
         top 0
         z-index -1
         width 100%
         transform translate3d(0,-100%,0)
         &.fold-leave-active,&.fold-enter-active
           transition all 0.5s
         &.fold-enter,&.fold-leave-active
           transform translate3d(0,0,0)  
         .list-header
            height 40px
            line-height 40px
            padding 0 18px
            background-color #f3f5f7 
            border-bottom 1px solid rgba(7,17,27,0.1)
            .title
               float left
               font-size 14px
               color rgb(7,17,27)
            .empty
               float right 
               font-size 12px
               color rgb(0,160,220)    
         .list-content
            padding  0 18px
            max-height 198px
            overflow hidden
            background-color #fff
            .food 
              position relative
              padding 12px 0
              box-sizing border-box
              border-1px(rgba(7,17,27,0.1)) 
              .name
                line-height 24px
                font-size 14px
                color rgb(7,17,27)
              .price
                position absolute   
                right 90px
                bottom 12px
                line-height 24px
                font-size 14px
                font-weight 700
                color rgb(240,20,20)
              .cartcontrol-wrapper
                position absolute
                right 0
                bottom 6px

</style>

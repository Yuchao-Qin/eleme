<template>
 <div class="page">
 <span class="icon" :class="classMap[seller.supports[0].type]"></span>
 </div>
</template>

<script>
 export default {
   props: ['seller'],  
   data() {
     return {
      classMap:['decrease','discount','special','invoice','guarantee']
     }

   },
   components: {
      
   },
   created () {
       console.log(this.seller)
   }
 }
</script>

<style scoped lang="stylus">
  @import "../../common/stylus/mixin"
  .icon
    display inline-block
    width 16px
    height 16px
    vertical-align top
    margin-right 16px
    background-size 16px 16px
    background-repeat  no-repeat
    &.decrease
        bg-image('decrease_2')
    &.discount
        bg-image('discount_2')
    &.guarantee
        bg-image('guarantee_2')
    &.invoice
        bg-image('invoice_2')
    &.special  
        bg-image('special_2')    
</style>

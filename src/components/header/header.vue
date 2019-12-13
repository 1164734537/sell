<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img width="64px" height="64px" :src="seller.avatar" alt="">
      </div>
      <div class="content">
          <div class="title">
            <span class="brand"></span>
            <span class="name">{{seller.name}}</span>
          </div>
          <div class="description">
            {{seller.description}}/{{seller.deliveryTime}}分钟送达
          </div>
          <div v-if="seller.supports" class="support">
            <span class="icon" :class="classMap[seller.supports[0].type]"></span>
            <span class="text">{{seller.supports[0].description}}</span>
          </div>
      </div>
     <div v-if="seller.supports" class="support-count">
       <span class="count">{{seller.supports.length}}个</span>
       <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper">
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
export default {
  props: {
    seller: {
      type: Object
    }
  },
  created() {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/index.styl'
  .header
    background : #999
    color:#fff
    .content-wrapper
      position: relative
      display flex
      padding 24px 12px 18px 24px
      .avatar
        img
          font-size: 0
          border-radius :2px
      .content
        margin-left :16px
        .title
          margin: 2px 0 8px 0
          // background :red
          display:flex
          align-items:center
          .brand
            display:inline-block
            width: 30px
            height: 18px
            bg-image(brand)
            background-size:cover
            background-repeat: no-repeat
          .name
            margin-left: 6px
            font-size: 18px
            font-weight: bold
        .description
           margin: 8px 0 10px 0
           font-size:12px
        .support
          margin-bottom :2px
          display: flex
          align-items: start
          .icon
            display: inline-block
            width: 12px
            height 12px
            margin-right:4px
            // bg-image(decrease_1)
            // background-size :cover
            background-size :12px 12px
            background-repeat: no-repeat
            &.decrease
              bg-image('decrease_1')
            &.discount
              bg-image('discount_1')
            &.guarantee
              bg-image('guarantee_1')
            &.invoice
              bg-image('invoice_1')
            &.special
              bg-image('special_1')
          .text
            font-size: 10px
            line-height: 12px
      .support-count
        position :absolute
        right 12px
        bottom: 18px
        padding 0 8px
        height: 24px
        line-height 24px
        border-radius: 14px
        background-color:rgba(0,0,0,.2)
        // text-align: center
        // display: flex
        // align-items: baseline
        .count
          font-size: 10px
        .icon-keyboard_arrow_right
          // margin-left: 2px
          display:inline-block
          font-size: 10px

    //     span
    //       display: inline-block
    //       font-size:10px
    //       // line-height :12px
    //     span+span
    //       margin-left: 2px
    // .bulletin-wrapper
    //   width 100%
    //   height 28px
    //   // padding: 0 12px
    //   display flex
    //   justify-content space-between
    //   align-items center
    //   background-color:rgb(7,17,27,0.1)
    //   // background :red
    //   .icon
    //     margin-left: 12px
    //     display:inline-block
    //     width:22px
    //     height:12px
    //     bg-image(bulletin)
    //     background-size :cover
    //     background-repeat: no-repeat
    //   .text
    //     width: 84%
    //     font-size:10px
    //     line-height 28px
    //     margin-left : 4px
    //     margin-right: 4px
    //     overflow :hidden
    //     text-overflow:ellipsis
    //     white-space:nowrap
    //   .btn{
    //     margin-right: 12px
    //   }
</style>

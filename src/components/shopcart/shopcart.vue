<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo">
            <i class="icon-shopping_cart"></i>
          </div>
        </div>
        <div class="price">&#165;0元</div>
        <div class="desc">另需配送费 &#165;{{ deliveryPrice }}元</div>
      </div>
      <div class="content-right">
        <div class="pay">
          &#165;{{ minPrice }}元起送
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .shopcart
    position: fixed
    z-index: 50
    left: 0
    bottom: 0
    width: 100%
    height: 48px
    // background: #000
    .content
      display: flex
      background: #141d27
      font-size: 0px
      color:rgba(255,255,255,.4)
      .content-left
        flex: 1
        .logo-wrapper
          display:inline-block
          vertical-align: top
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height:56px
          // 转化为IE盒模型方便使用
          box-sizing: border-box
          border-radius:50%
          background: #141d27
          .logo
            width: 100%
            height: 100%
            border-radius: 50%
            background: #2b343c
            text-align:center
            .icon-shopping_cart
              line-height:44px
              font-size:24px
              color: #80858a
        .price
          display:inline-block
          vertical-align: top
          margin-top: 12px
          line-height: 24px
          padding-right: 12px
          box-sizing: border-box
          border-right: 1px solid rgba(255,255,255,.1)
          font-size: 16px
          font-weight: 700
        .desc
          display:inline-block
          vertical-align: top
          margin: 12px 0 0 12px
          line-height: 24px
          font-size: 10px
      .content-right
        flex:0 0 105px
        width: 105px
        .pay
          // margin:0 8px
          height: 48px
          line-height: 48px
          text-align: center
          font-size: 12px
          font-weight: 700
          background: #2b333b

</style>

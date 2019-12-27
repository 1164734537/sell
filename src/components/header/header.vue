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
     <div v-if="seller.supports" class="support-count" @click="showDetail">
       <span class="count">{{seller.supports.length}}个</span>
       <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="buulletin-title"></span>
      <span class="bulletin-text">{{ seller.bulletin }}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" style="width: 100%; height: 100%;">
    </div>
    <transition name="fade">
      <div v-show="detailShow" class="detail">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="name">{{seller.name}}</h1>
            <div class="star-wrapper">
              <star :size='48' :score="seller.score"></star>
            </div>
            <div class="title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-if="seller.supports" class="supports">
              <li class="support-item" v-for="(item,index) in seller.supports" :key="index">
                <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                <span class="text">{{seller.supports[index].description}}</span>
              </li>
            </ul>
            <div class="title">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <div class="bulltin">
              <p class="content">{{ seller.bulletin }}</p>
            </div>
          </div>
        </div>
        <div class="detail-close">
          <i class="icon-close" @click="hideDetail"></i>
        </div>
      </div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
import star from '../star/star';
export default {
  props: {
    seller: {
      type: Object
    }
  },
  components: {
    'star': star
  },
  data() {
    return {
      detailShow: false
    };
  },
  methods: {
    showDetail: function() {
      this.detailShow = true;
    },
    hideDetail: function() {
      this.detailShow = false;
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
    position: relative
    background-color:rgba(7,17,27,.5)
    color:#fff
    overflow:hidden
    .content-wrapper
      position: relative
      display flex
      padding 24px 12px 18px 24px
      .avatar
        font-size: 0
        img
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
        bottom: 14px
        padding 0 8px
        height: 24px
        line-height 24px
        border-radius: 14px
        background-color:rgba(0,0,0,.2)
        text-align: center
        font-size: 0px
        display: flex
        align-items:center
        // align-items: baseline
        .count
          display:inline-block
          // vertical-align: center
          font-size: 10px
        .icon-keyboard_arrow_right
          margin-left: 2px
          display:inline-block
          line-height: 24px
          font-size: 10px
    .bulletin-wrapper
      position:relative
      height: 28px
      line-height: 28px
      padding: 0 22px 0 12px
      white-space: nowrap
      overflow: hidden
      text-overflow:ellipsis
      // font-size:0
      word-spacing:-4px
      background-color: rgba(7,17,27,.2)
      .buulletin-title
        display:inline-block
        vertical-align:top
        margin-top:7px
        width:22px
        height:12px
        bg-image(bulletin)
        background-size: 22px 12px
        background-repeat: no-repeat
      .bulletin-text
        margin: 0 4px
        font-size: 10px
        vertical-align:top
      .icon-keyboard_arrow_right
        position: absolute
        font-size: 10px
        right: 12px
        top: 9px
    .background
      position: absolute
      left: 0
      top: 0
      width:100%
      height:100%
      z-index:-1
      filter:blur(10px)
    .detail
      position: fixed
      z-index: 100
      top:0
      left:0
      width: 100%
      height: 100%
      overflow: auto
      opacity: 1
      // 仅适用于ios
      backdrop-filter: blur(10px)
      background-color:rgba(7,17,27,.8) //渐变结束后的效果
      //重点开始 定义渐变的进入和退出都经历0.5s
      &.fade-enter-active,&.fade-leave-active //定义进入过渡生效时的状态 定义离开过渡生效时的状态
        transition: all .5s
      &.fade-enter,&.fade-leave-to //定义的是过渡开始,和过渡结束后的状态
        opacity: 0
        background-color: rgba(7,17,27,0)
      .detail-wrapper
        min-height: 100%
        width: 100%
        .detail-main
          margin-top:64px
          padding-bottom: 64px
          .name
            font-size: 16px
            line-height: 16px
            font-weight: 700
            text-align: center
          .star-wrapper
            margin-top: 16px
            padding: 2px 0
            text-align: center
          .title
            width: 80%
            display:flex
            margin 28px auto 24px auto
            .line
             // height 1px
             flex: 1
             position: relative;
             top:-6px
             border-bottom: 1px solid rgba(255,255,255,.2)
            .text
             padding: 0 12px
             font-weight:700
             font-size: 14px
          .supports
            width 80%
            margin: 0 auto
            .support-item
              padding: 0 12px
              margin-bottom: 12px
              font-size:0
              &:last-child
                margin-bottom: 0px
              .icon
                display:inline-block
                width:16px
                height:16px
                vertical-align: top
                margin-right: 6px
                background-size: 16px 16px
                background-repeat: no-repeat
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
              .text
                font-size:12px
                line-height: 16px
          .bulltin
            width: 80%
            margin: 0 auto
            padding: 0 12px
            p
             font-size:12px
             line-height:24px
      .detail-close
        position: relative
        width: 32px
        height: 32px
        margin: -64px auto 0 auto
        clear: both
</style>

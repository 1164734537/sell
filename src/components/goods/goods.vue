<template>
  <div class="goods">
    <div class="menu-wrapper">
      <ul>
        <li  class="menu-item" v-for="item in goods" :key="item.name">
          <span class="text border-1px">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper">
      <ul>
        <li  v-for="item in goods" class="food-list" :key="item.name">
          <h1 class="title">{{ item.name }}</h1>
          <ul>
            <li v-for="food in item.foods" :key="food.name" class="food-item border-1px">
              <div class="icon">
                 <img :src="food.icon" alt="">
              </div>
              <div class="content">
                <h2 class="name">{{ food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span>月售{{food.sellCount}}份</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span>&#165;{{food.price}}</span>
                  <span v-show="food.oldPrice">&#165;{{food.oldPrice}}</span>
                </div>
                <div class="count">

                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
const ERR_OK = 0;
export default{
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      goods: {}
    };
  },
  created() {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    this.$axios.get('/api/goods')
      .then(res => {
        // console.log(res.data.data);
        if (res.data.errno === ERR_OK) {
          this.goods = res.data.data;
          console.log(this.goods);
        }
      })
      .catch(error => {
        console.log(error);
      });
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
 @import '../../common/stylus/index.styl'
  .goods
    display: flex
    position: absolute
    top: 174px
    bottom:46px
    // background : darkred
    width 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width:80px
      background: #f3f5f7
      .menu-item
        // 用table 布局 实现垂直居中
        display: table
        width: 56px
        height: 54px
        line-height:14px
        padding: 0 12px
        .icon
          display: inline-block
          vertical-align: top
          width: 12px
          height 12px
          margin-right:2px
          // bg-image(decrease_1)
          // background-size :cover
          background-size :12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          display:table-cell
          width 56px
          vertical-align: middle
          border-1px(rgba(7,17,27,.1))
          font-size: 12px
    .foods-wrapper
      flex: 1
      .title
        padding-left: 14px
        height: 26px
        line-height: 26px
        border-left: 2px solid #d9dde1
        font-size: 12px
        color: rgb(147,153,159)
        background-color: #f3f5f7
      .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px(rgba(7,17,27,.1))
        &:last-child
         border-none()
         margin-bottom: 0px
        .icon
          flex: 0 0 57px
          img
           width: 100%
</style>

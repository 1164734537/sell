<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease " v-show="food.count>0" @click="decreaseCart">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="count" v-show="food.count>0">{{ food.count }}</div>
    <div class="cart-add icon-add_circle" @click="addCart"></div>
  </div>
</template>

<script>
import Vue from 'vue';
// Vue.set() 方法
// 参数1： 要修改的对象
// 参数2： 属性
// 参数3： 属性的值是啥
// 返回值：已经修改好的值
export default{
  props: {
    food: {
      type: Object
    }
  },
  created() {
    console.log(this.food);
  },
  methods: {
    addCart(event) {
      if (!event._constructed) {
        return;
      }
      if (!this.food.count) {
        Vue.set(this.food, 'count', 1);
        // this.food.count = 1;
      } else {
        this.food.count++;
      }
      // console.log('1');
    },
    decreaseCart(event) {
      if (!event._constructed) {
        return;
      }
      if (this.food.count > 0) {
        // Vue.set(this.food, 'count', 1);
        this.food.count--;
      }
    }
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size: 0
    .cart-decrease
      display:inline-block
      padding: 6px
      font-size: 0
      // 这是为了动画设置的 参数
      opacity: 1
      transform: translate3D(0,0,0)
        // background-color: rgba(7,17,27,0)
      .inner
        display: inline-block
        line-height: 24px
        font-size: 24px
        color:rgb(0,160,220)
        // 过渡的预设
        transition: all 0.4s linear
        // 默认旋转
        transform: rotate(0)
      &.move-enter-active,&.move-leave-active  //定义进入过渡生效时的状态 定义离开过渡生效时的状态
        transition: all 0.4s linear
      &.move-enter,&.move-leave-active //定义的是过渡开始进入,和 过渡离开这个过程
         opacity: 0
         transform: translate3d(24px, 0, 0)
         .inner
          transform: rotate(180deg)
    .count
      display:inline-block
      vertical-align: top
      width: 12px
      padding-top: 6px
      line-height: 24px
      font-size: 10px
      text-align: center
      color: rgb(147,153,159)
    .cart-add
      display:inline-block
      padding: 6px
      line-height: 24px
      font-size: 24px
      color:rgb(0,160,220)
</style>

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
        <div class="price" :class="{'highlight':totalPrice>0}">&#165;{{ totalPrice }}元</div>
        <div class="desc">另需配送费 &#165;{{ deliveryPrice }}元</div>
      </div>
      <div class="content-right">
        <div class="pay" :class="payClass">
         <!-- &#165;{{ minPrice }}元起送 -->
          {{ payDesc }}
        </div>
      </div>
      <div class="ball-container">
        <div v-for="ball in balls">
         <transition name="drop"
             @before-enter="beforeDrop"
              @enter="dropping"
              @after-enter="afterDrop"
          >
            <div class="ball" v-show="ball.show">
                <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data(){
    return{
      balls: [ //生成一个动画小球的div,并且生成五个小球,五个是为了生成一定数量的小球来作为操作使用,按照小球动画的速度,一般来说五个也可以保证有足够的小球数量来运行动画
        {
          show: false  //维护当前小球的状态
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        }
      ],
      dropBalls: [], //dropBalls数组接收下落小球,用来存贮已经下落的小球
      fold: true //购物车详情列表默认折叠
    }
  },
  props: {
    selectFoods: {
      type: Array,
      default() {
        return [
          // {
          //   price: 10,
          //   count: 1
          // }
        ];
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  computed: {
    totalPrice() {
      let total = 0;
      this.selectFoods.forEach((food) => {
        total += food.price * food.count;
      });
      return total;
    },
    totalCount() {
      let count = 0;
      this.selectFoods.forEach((food) => {
        count += food.count;
      });
      return count;
    },
    payDesc() {
      if (this.totalPrice === 0) {
        return `¥ ${this.minPrice}元起送`;
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice;
        return `¥ 还差${diff}元起送`;
      } else {
        return '去结算';
      }
    },
    payClass() {
      if (this.totalPrice < this.minPrice) {
        return 'not-enought';
      } else {
        return 'enought';
      }
    }
  },
  methods: {
    // 当触发drop方法时小球开始掉落
    drop (el) {
      for (let i = 0; i < this.balls.length; i++) { // 遍历这5个小球
      let ball = this.balls[i]
      if (!ball.show) { // 当小球显示状态为隐藏时
       ball.show = true // 将这个小球的显示状态设置为true
       ball.el = el  // 将cartControl传过来的对象挂载到ball的el属性上
       this.dropBalls.push(ball) // 将这个小球放入到dropBalls数组中
       return
      }
      }
    },
    // js钩子函数
    //下面方法beforeDrop，dropping，afterDrop都是动画方法
        beforeDrop(el) {  //这个方法的执行是因为这是一个vue的监听事件,动画enter之前
            //把使用到的小球从起始位置（购物车位置）上升到添加按钮位置
            //在这时el与上面的drop（el）不是同一个el,这里的el代表的是类为ball的div及其子元素
            let count = this.balls.length;
            //先找到所有为true的小球（连续点击的情况）
            while (count--) { //循环执行并判断
                let ball = this.balls[count];
                if (ball.show) { //下落的小球
                    //外层元素是纵向的动画，内层元素是横向的动画
                    //el.getBoundingClientRect()这个方法返回一个矩形对象，
                    // 包含四个属性：left、top、right和bottom。分别表示元素的左，上，右和下分别相对【浏览器视窗】的位置。
                    //ball.el与el不是同一个，ball.el是指点击添加按钮时的那个el
                    let rect = ball.el.getBoundingClientRect(); //获取小球的相对于视口的位移(小球高度)
                    console.log('rrr');
                    console.log(rect);
                    let x = rect.left - 32; //当前按钮和购物车中小球的left的差值
                    //window.innerHeight文档显示区域的宽高,y表示的是按钮到购物车的高度
                    let y = -(window.innerHeight - rect.top - 22); //负数,因为是从左上角往下的的方向【】
                    el.style.display = '';
                    //el的动画偏移
                    el.style.webkitTransform = `translate3d(0,${y}px,0)`;
                    el.style.transform = `translate3d(0,${y}px,0)`;
                    //处理内层动画,确定小球的起始位置
                    let inner = el.getElementsByClassName('inner-hook')[0];
                    inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
                    inner.style.transform = `translate3d(${x}px,0,0)`;
                }
            }
        },
        dropping(el, done) { //动画enter进入
            //rf是为了触发浏览器的重排
            //*添加、删除、更新DOM节点
       // *通过display: none隐藏一个DOM节点-触发重排和重绘
           // *通过visibility: hidden隐藏一个DOM节点-只触发重绘，因为没有几何变化
           // *移动或者给页面中的DOM节点添加动画
           // *添加一个样式表，调整样式属性
            //*用户行为，例如调整窗口大小，改变字号，或者滚动。
        //*获取某些元素的属性（我使用的方法）
             let rf = el.offsetHeight;  //触发重绘html
            this.$nextTick(() => {  //回调函数异步执行，两个动画效果就不会卡顿了
                el.style.webkitTransform = 'translate3d(0,0,0)';
                el.style.transform = 'translate3d(0,0,0)';
                let inner = el.getElementsByClassName('inner-hook')[0];
                inner.style.webkitTransform = 'translate3d(0,0,0)';
                inner.style.transform = 'translate3d(0,0,0)';
                el.addEventListener('transitionend', done);
            });

        },
        afterDrop(el) { //动画进入之后
            let ball = this.dropBalls.shift();//完成一次动画就删除一个dropBalls的小球
            if (ball) {
                ball.show = false;
                el.style.display = 'none'; //隐藏小球
            }
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
            &.highlight
              background:rgb(0,160,220)
            .icon-shopping_cart
              line-height:44px
              font-size:24px
              color: #80858a
              &.highlight
                color: #fff
          .num
            position: absolute
            top: 0
            right: 0
            width: 24px
            height: 16px
            line-height: 16px
            text-align: center
            border-radius: 16px
            font-size: 9px
            font-weight: 700
            background: rgb(240,20,20)
            color: #fff
            box-shadow: 0 4px 8px 0 rgba(0,0,0,.4)
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
          &.highlight
            color: #fff
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
          &.not-enought
            background: #2b333b
          &.enought
            background: #00b43c
            color: #fff
      .ball-container
        .ball
          position: fixed
          left: 32px
          bottom: 22px
          z-index: 200
          transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
          .inner
            width: 16px
            height: 16px
            border-radius: 50%
            background: rgb(0, 160, 220)
            transition: all 0.4s linear

</style>

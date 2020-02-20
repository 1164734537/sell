<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menu">
      <ul>
        <li  class="menu-item" v-for="(item,index) in goods" :key="item.name" :class="{'current':currentIndex === index}" @click="selectMenu(index,$event)">
          <span class="text border-1px">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foods">
      <ul>
        <li  v-for="item in goods" class="food-list food-list-hook" :key="item.name">
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
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">&#165;{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">&#165;{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol @cart-add="cartAdd" :food = "food"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <!-- 购物车区域 -->
    <shopcart :select-foods="selectFoods"
              :delivery-price="seller.deliveryPrice"
              :minPrice="seller.minPrice"
              ref="shopcart"
              ></shopcart>
  </div>
</template>

<script type="text/ecmascript-6">
import BScroll from 'better-scroll';
import shopcart from '@/components/shopcart/shopcart';
import cartcontrol from '@/components/cartcontrol/cartcontrol';
const ERR_OK = 0;
export default{
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      goods: [],
      // 存高度区间的数组
      listHeight: [],
      scrollY: 0
    };
  },
  computed: {
    // 通过实时监听,判断左侧索引的位置
    currentIndex() {
      for (let i = 0; i < this.listHeight.length; i++) {
        // 如同[0,+∞)这样的区间
        let height1 = this.listHeight[i];
        let height2 = this.listHeight[i + 1];
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i;
        }
      }
      return 0;
    },
    selectFoods() {
      let foods = [];
      // 遍历goods获得 foods
      this.goods.forEach((good) => {
        // 再遍历 good.foods  拿到 food
        good.foods.forEach((food) => {
          // 判断当前 food.count 是否存在 ,有则是选中的
          if (food.count) {
            // 有则push到数组
            foods.push(food);
          }
        });
      });
      return foods;
    }
  },
  created() {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    this.$axios.get('/api/goods')
      .then(res => {
        // console.log(res.data.data);
        if (res.data.errno === ERR_OK) {
          this.goods = res.data.data;
          // console.log(this.goods);
          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
          });
        }
      })
      .catch(error => {
        console.log(error);
      });
  },
  methods: {
    selectMenu(index, event) {
      // console.log(event);
      // 监听到浏览器的就 return 掉
      if (!event._constructed) {
        return;
      }
      // console.log(index);
      // 获取到index 值后,对右侧的区间进行定位
      let foodList = this.$refs.foods.getElementsByClassName('food-list-hook');
      let el = foodList[index];
      // scrollToElement 是better-scroll 下的一个api
      // scrollToElement(el, time, offsetX, offsetY, easing)
      // 滚动到某个元素，el（必填）表示 dom 元素，time 表示动画时间，offsetX 和 offsetY 表示坐标偏移量，easing 表示缓动函数
      this.foodsScroll.scrollToElement(el, 300);
    },
    _initScroll() {
      this.menuScroll = new BScroll(this.$refs.menu, {
        // 默认是阻止点击事件的
        click: true
      });
      this.foodsScroll = new BScroll(this.$refs.foods, {
        // probeType: 1 滚动的时候会派发scroll事件，会截流。2滚动的时候实时派发scroll事件，不会截流。 3除了实时派发scroll事件，在swipe的情况下仍然能实时派发scroll事件
        probeType: 3,
        // 设置点击,是为了让count可以被点击到
        click: true
      });
      // 监听滚动事件
      this.foodsScroll.on('scroll', (pos) => {
        // console.log(pos.y); 取 正整数,
        this.scrollY = Math.abs(Math.round(pos.y));
      });
    },
    _calculateHeight() {
      // 获取每个li
      let foodList = this.$refs.foods.getElementsByClassName('food-list-hook');
      // console.log(foodList)
      // 定义一个临时变量
      let height = 0;
      // 把它push上数组 区间数组
      this.listHeight.push(height);
      for (let i = 0; i < foodList.length; i++) {
        // 这里是获得每一项li
        let item = foodList[i];
        // 得到每一项li的高度,并进行累加(因为是区间高度)
        height += item.clientHeight;
        this.listHeight.push(height);
      }
    },
    _drop(target) {
      // 在goods.vue定义 _drop方法将cartcontrol传递过来的target对象再传递给shopcart
      // console.log(target);
      this.$nextTick(() =>{
       // 使用$nextTick优化体验
       //target代表点击的添加商品按钮icon-add_circle
        this.$refs.shopcart.drop(target); //访问到$refs.shopcart组件中的方法drop
      })
    },
    // 点击加号按钮触发事件
    cartAdd(target) { // 点击加号按钮触发事件
      // console.log(target);
      // 调用_drop方法
      this._drop(target);
    }
  },
  components: {
    shopcart,
    cartcontrol
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
        &.current
          position: relative
          z-index: 10
          margin-top: -1px
          background : #fff
          .text
            font-weight: 700
            border-none()
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
          font-size:0px
          margin-right:10px
          img
           width: 57px
           height:57px
        .content
          flex: 1
          .name
            margin: 2px 0 8px 0
            font-size: 14px
            color:rgb(7,17,27)
            line-height: 14px
            // font-weight: 700
          .desc,.extra
            font-size:10px
            color:rgb(147,153,159)
            line-height: 10px
            // margin:8px
          .desc
            margin-bottom: 8px
            line-height:12px
          .extra
            word-spacing:-4px
            & .count
             margin-right: 12px
          .price
              font-weight: 700
              line-height:24px
              .now
                 margin-right:8px
                 font-size:14px
                 color:rgb(240,20,20)
               .old
                 text-decoration: line-through
                 font-size:10px
                 color:rgb(147,153,159)
          .cartcontrol-wrapper
              // position: absolute
              position: absolute
              right: 0
              bottom: 12px
              // width: 50px
              // height: 50px
              // background: red
</style>

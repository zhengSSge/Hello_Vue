<template>
  <!--该组件是通过外部传入数据计算总价以及数量的-->
  <!--该组件是通过外部传入数据计算总价以及数量的-->
  <!--该组件是通过外部传入数据计算总价以及数量的-->
  <div class="shopchar">
    <div class="content" @click="toggleShow">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{ 'light' : totalPrice > 0 }">
            <i class="icon-close" :class="{ 'light' : totalPrice > 0 }"></i>
          </div>
          <div class="num" v-show="totalPrice > 0 ">
            {{totalCount}}
          </div>
        </div>
        <div class="price" :class="{ 'light' : totalPrice > 0 }">￥{{totalPrice}}元</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right">
        <div class="pay" :class="payClass" @click.stop.prevent="goPay">
          {{payDesc}}
        </div>
      </div>
    </div>
    <transition name="fold">
      <div class="shopcart-list" v-show="listShow">
        <div class="list-header">
          <h1 class="title">购物车</h1>
          <span class="empty" @click="empty">清空</span>
        </div>
        <div class="list-content" ref="listContent">
          <ul>
            <li class="food" v-for="food in selectFoods">
              <span class="name">{{food.name}}</span>
              <div class="price">
                <span>￥{{food.price * food.count}}</span>
              </div>
              <div class="cartcontrol-wrapper">
                <cartcontrol :food="food"></cartcontrol>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </transition>
    <transition name="fade">
      <div class="list-mask" @click="hideList" v-show="listShow"></div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from '../cartcontrol/cartcontrol';

  export default {
    name: 'shopchar',
    props: {
      selectFoods: {
        // Array和Object, default是函数
        type: Array,
        default() {
          return [];
        }
      },
      minPrice: {
        // 满xx起送
        type: Number,
        default: 0
      },
      deliveryPrice: {
        // 配送费
        type: Number,
        default: 0
      }
    },
    data() {
      return {
        balls: [
          // 小球动画开关
          {show: false},
          {show: false},
          {show: false},
          {show: false},
          {show: false}
        ],
        dropBalls: [],
        // 订单详情页开关
        fold: true
      };
    },
    computed: {
      totalPrice() {
        // 总价计算
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total;
      },
      totalCount() {
        // 件数计算
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count;
        });
        return count;
      },
      payDesc() {
        // 配送费倒计
        if (this.totalPrice === 0) {
          return '￥' + this.minPrice + '元起送';
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice;
          return `￥差${diff}元起送`;
        } else {
          return '结算';
        }
      },
      payClass() {
        // 配送费倒计样式高亮计算
        if (this.totalPrice >= this.minPrice) {
          return 'enough';
        } else {
          return 'not-enough';
        }
      },
      listShow() {
        // 合计大于0时 this.fold 返回true
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        // 合计小于0时 this.fold 返回false
        let show = !this.fold;
        if (show) {
          // 订单详情页滚动、派发事件
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.listContent, {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
        return show;
      }
    },
    methods: {
      drop(el) {
        // goods 调用传入event (小球动画懒得实现)
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) {
            ball.show = true;
            ball.el = el;
            this.dropBalls.push(ball);
            return;
          }
        }
      },
      toggleShow() {
        // this.fold为false时不向下执行，否则取反
        if (!this.totalCount) {
          return;
        }
        this.fold = !this.fold;
      },
      empty() {
        // 清空订单
        this.selectFoods.forEach((food) => {
          food.count = 0;
        });
      },
      hideList() {
        // 涂层点击关闭
        this.fold = true;
      },
      goPay() {
        // 支付
        if (this.totalPrice < this.minPrice) {
          return;
        }
        alert(`共计${this.totalPrice}元`);
      }
    },
    components: {
      cartcontrol
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/maxin.styl"
  .shopchar
    position: fixed
    left: 0
    bottom: 0
    z-index: 50
    width: 100%
    height: 48px
    .content
      display: flex
      background: #141d27
      font-size: 0
      color: rgba(255, 255, 255, 0.4)
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height: 56px
          box-sizing: border-box
          vertical-align: top
          border-radius: 50%
          background: #141d27
          .logo
            height: 100%
            width: 100%
            border-radius: 50%
            background: #2b343c
            text-align: center
            &.light
              background rgb(0, 160, 228)
            .icon-close
              color #80858a
              font-size 24px
              line-height 48px
              &.light
                color #fff
            .icon-close
              font-size: 24px
              line-height: 48px
              color: rgba(255, 255, 255, 0.4)
          .num
            position absolute
            top 0
            right 0
            width 24px
            height 16px
            line-height 16px
            text-align center
            border-radius 16px
            font-size 9px
            font-weight 700
            color #fff
            background rgb(240, 20, 20)
            box-shadow 0 4px 8px 0 rgb(0, 0, 0, 0.4)
        .price
          display: inline-block
          vertical-align: top
          box-sizing: border-box
          margin-top: 12px
          line-height: 24px
          padding-right: 12px
          border-right: 1px solid rgba(255, 255, 255, 0.1)
          font-size: 16px
          font-weight: 700
          &.light
            color #fff
        .desc
          display: inline-block
          vertical-align: top
          margin-top: 12px
          line-height: 24px
          padding-left: 12px
          font-size: 10px
      .content-right
        width: 105px
        .pay
          height 48px
          line-height 48px
          font-size 15px
          font-weight 700
          color rgba(255, 255, 255, 0.4)
          text-align center
          background #2b333b
          &.not-enough
            background #2b333b
          &.enough
            background: #00b43c;
            color: #fff;
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
    .shopcart-list
      position: absolute
      left: 0
      top: 0
      z-index: -1
      width: 100%
      transform: translate3d(0, -100%, 0)
      &.fold-enter-active, &.fold-leave-active
        transition: all 0.5s
      &.fold-enter, &.fold-leave-active
        transform: translate3d(0, 0, 0)
      .list-header
        height: 40px
        line-height: 40px
        padding: 0 18px
        background: #f3f5f7
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        .title
          float: left
          font-size: 14px
          color: rgb(7, 17, 27)
        .empty
          float: right
          font-size: 12px
          color: rgb(0, 160, 220)
      .list-content
        padding: 0 18px
        max-height: 217px
        overflow: hidden
        background: #fff
        .food
          position: relative
          padding: 12px 0
          box-sizing: border-box
          border-1px(rgba(7, 17, 27, 0.1))
          .name
            line-height: 24px
            font-size: 14px
            color: rgb(7, 17, 27)
          .price
            position: absolute
            right: 90px
            bottom: 12px
            line-height: 24px
            font-size: 14px
            font-weight: 700
            color: rgb(240, 20, 20)
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom: 6px
    .list-mask
      position: fixed
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: -20
      backdrop-filter: blur(10px)
      opacity: 1
      background: rgba(7, 17, 27, 0.6)
      &.fade-enter-active, &.fade-leave-active
        transition: all 0.5s
      &.fade-enter, &.fade-leave-active
        opacity: 0
        background: rgba(7, 17, 27, 0)
</style>

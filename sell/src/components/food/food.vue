<template>
  <transition name="move">
    <div v-show="showFlag" class="food" ref="food">
      <div class="food-content">
        <div class="image-header">
          <img :src="food.image">
          <div @click="_hide" class="back">
            <i class="icon-add_circle"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}</span><span class="rating">好评率{{food.rating}}%</span>
          </div>
        </div>
        <div class="price">
          <span class="price-x">
            ￥<span class="price-xx">{{food.price}}</span>
          </span>
          <span class="price-y" v-show="food.oldPrice"><span class="price-yy">￥</span>{{food.oldPrice}}</span>
        </div>
        <div class="cartcontrol-wrapper">
          <cartcontrol  :food="food"></cartcontrol>
        </div>
      </div>
    </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from '../cartcontrol/cartcontrol';

  export default {
    props: {
      food: Object
    },
    data() {
      return {
        showFlag: false
      };
    },
    created() {
    },
    methods: {
      show() {
        this.showFlag = true;
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.food, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      },
      _hide() {
        this.showFlag = false;
      }
    },
    components: {
      cartcontrol
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/maxin.styl";

  .food
    position fixed
    left 0
    top 0
    bottom 48px
    width 100%
    background #fff
    z-index 30
    transform: translate3d(0, 0, 0)
    &.move-enter-active, &.move-leave-active
      transition: all 0.3s linear
    &.move-enter, &.move-leave-active
      transform: translate3d(100%, 0, 0)
    .image-header
      position relative
      width 100%
      height 0
      padding-top 100%
      img
        position absolute
        left 0
        top 0
        width 100%
        height 100%
      .back
        position absolute
        top 10px
        left 0
        .icon-add_circle
          padding 10px
          font-size 20px
          color #fff

    .content
      padding 18px
      .title
        font-size 14px
        font-weight 700
        color rgb(7, 17, 27)
        line-height 14px
        margin-bottom 8px
      .detail
        font-size 10px
        color rgb(147, 153, 159)
        line-height 10px
        .sell-count
          margin-right 12px
    .price
      padding 0 0 18px 18px
      line-height 24px
      .price-x
        font-weight normal
        font-size 10px
        color rgb(240, 20, 20)
        .price-xx
          font-size 14px
          color rgb(240, 20, 20)
          font-weight 700
      .price-y
        text-decoration line-through
        font-size 10px
        color rgb(147, 153, 159)
        font-weight 700
        .price-yy
          font-weight normal
</style>

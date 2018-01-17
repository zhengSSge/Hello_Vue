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
          <div class="price">
          <span class="price-x">
            ￥<span class="price-xx">{{food.price}}</span>
          </span>
            <span class="price-y" v-show="food.oldPrice"><span class="price-yy">￥</span>{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol :food="food"></cartcontrol>
          </div>
          <div @click.stop.prevent="addFirst" class="buy" v-show="!food.count || food.count===0">
            加入购物车
          </div>
        </div>
        <split v-show="food.info"></split>
        <div class="info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
      </div>
    </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import Vue from 'vue';
  import cartcontrol from '../cartcontrol/cartcontrol';
  import split from '../split/split';

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
      },
      addFirst(event) {
        if (!event._constructed) {
          return;
        }
        Vue.set(this.food, 'count', 1);
      }
    },
    components: {
      cartcontrol,
      split
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
      position relative
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
        margin-bottom 18px
        .sell-count
          margin-right 12px
      .price
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
      .cartcontrol-wrapper
        position: absolute
        right: 12px
        bottom: 12px
      .buy
        position: absolute
        right: 18px
        bottom: 18px
        z-index: 10
        height: 24px
        line-height: 24px
        padding: 0 12px
        box-sizing: border-box
        border-radius: 12px
        font-size: 10px
        color: #fff
        background: rgb(0, 160, 220)
        opacity: 1

    .info
      padding: 18px
      .title
        line-height: 14px
        margin-bottom: 6px
        font-size: 14px
        color: rgb(7, 17, 27)
      .text
        line-height: 24px
        padding: 0 8px
        font-size: 12px
        color: rgb(77, 85, 93)
</style>

<template>
  <!--该组件是 添加商品减少商品的-->
  <div class="cartControl">
    <transition name="move">
      <div class="cart-left" v-show="this.food.count>0" @click="deleteCart" transition="move">
        <span class="inners icon-shopping_cart"></span>
      </div>
    </transition>
    <div class="cart-content" v-show="this.food.count>0">{{food.count}}</div>
    <div class="cart-right icon-thumb_down" @click="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';

  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart(event) {
        if (!event._constructed) {
          return;
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1);
        } else {
          this.food.count++;
        }
        this.$emit('add', event.target);
      },
      deleteCart(event) {
        if (!event._constructed) {
          return;
        }
        if (this.food.count) {
          this.food.count--;
        }
      }
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">

  .cartControl
    font-size 0
    .cart-left
      display inline-block
      padding 4px
      color rgb(0, 160, 220)
      transition all 0.4s linear
      &.move-transition
        opacity 1
        transform translate3d(0, 0, 0)
      .inners
        display inline-block
        line-height 24px
        font-size 24px
        color rgb(0, 160, 220)
        transition all 0.4s linear
        transform rotate(0)
      &.move-enter, &.move-leave
        opacity 0
        transition all 0.4s linear
        transform translate3d(24px, 0, 0)
      .inners
        transform rotate(180deg)
    .cart-content
      display inline-block
      vertical-align top
      padding-top 6px
      line-height 24px
      text-align center
      font-size 10px
      color rgb(147, 153, 159)
    .cart-right
      display inline-block
      padding 4px
      line-height 24px
      font-size 24px
      color rgb(0, 160, 220)
</style>

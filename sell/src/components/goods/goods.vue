<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item" :class="{ 'current': currentIndex === index }"
            @click="selectMenu(index,$event)">
        <span class="text border-1px">
          <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
        </span>
        </li>
      </ul>
    </div>
    <div class="goods-wrapper" ref="goodsWrapper">
      <ul>
        <li v-for="item in goods" class="foods-list foods-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item border-1px">
              <div class="icon">
                <img width="57" height="57" :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span>月售{{food.sellCount}}份</span><span class="rating">好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="price-x">￥<span class="price-xx">{{food.price}}</span></span><span class="price-y"
                                                                                                  v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartControl-wrapper">
                  <quantity @add="addFood" :food="food"></quantity>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <Clearing ref="shopcart" :select-foods='selectFoods' :minPrice="seller.minPrice" :deliveryPrice="seller.deliveryPrice"></Clearing>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import Clearing from '../shopchar/shopchar';
  import quantity from '../cartcontrol/cartcontrol';

  const ERR_OK = 0;
  export default {
    name: 'goods',
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        target: ''
      };
    },
    // 计算数据，根据数据改变而改变
    computed: {
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i;
          }
        }
        return 0;
      },
      // 数据转存
      selectFoods() {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    created() {
      // 插入dom操作,加载完页直接执行
      this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special'];
      this.$http.get('/api/goods').then(response => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.goods = response.data;
          this.$nextTick(() => {
            // dom操作调用
            this._initScrolls();
            this._calculateHeight();
          });
        }
      });
    },
    methods: {
      // 绑定事件触发函数放这里
      selectMenu(index, event) {
        if (!event._constructed) {
          return;
        }
        let foodList = this.$refs.goodsWrapper.getElementsByClassName('foods-list-hook');
        let el = foodList[index];
        this.goodsWrapper.scrollToElement(el, 300);
      },
      addFood(target) {
        // 接收cartcontrol递参数 父组件接收子组件状态
        this._drop(target);
      },
      _drop(el) {
        this.$nextTick(() => {
          // 调用shopcart组件内drop方法，将cartcontrol组件数据递入shopcart组件
          this.$refs.shopcart.drop(el);
        });
      },
      _initScrolls() {
        // 接收ref=''；
        this.menuWrapper = new BScroll(this.$refs.menuWrapper, {
          click: true
        });
        this.goodsWrapper = new BScroll(this.$refs.goodsWrapper, {
          click: true,
          probeType: 3 // 鼠标滚动实时刷新
        });
        this.goodsWrapper.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      _calculateHeight() {
        let foodList = this.$refs.goodsWrapper.getElementsByClassName('foods-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          let time = foodList[i];
          height += time.clientHeight;
          this.listHeight.push(height);
        }
      }
    },
    components: {
      // 注册组件
      Clearing,
      quantity
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/maxin.styl"
  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 88px
      width: 88px
      background: #f3f5f7
      .menu-item
        display: table
        height: 54px
        width: 56px
        line-height 14px
        font-size: 12px
        padding: 0 16px
        &.current
          position: relative
          margin-top: -1px
          z-index: 10
          background: #fff
          font-weight: 700
          border: none
          font-size: 12px
          line-height 14px
        .icon
          display: inline-block
          width: 12px
          height: 12px
          margin-right: 2px
          background-size: 12px 12px !important
          background-repeat: no-repeat !important
          &.decrease
            bg-image: ('decrease_3')
          &.discount
            bg-image: ('discount_3')
          &.guarantee
            bg-image: ('guarantee_3')
          &.invoice
            bg-image: ('invoice_3')
          &.special
            bg-image: ('special_3')
        .text
          display: table-cell
          width: 56px
          vertical-align: middle
          border-1px(rgba(7, 17, 27, 0.1))
          font-size: 12px
    .goods-wrapper
      flex: 1
      .title
        padding-left: 14px
        height: 26px
        line-height: 26px
        border-left: 2px solid #d9dde1
        font-size: 12px
        color: rgb(147, 153, 159)
        background: #f3f5f7
      .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
          margin-bottom: 0
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          .name
            margin: 2px 0 8px 0
            height: 14px
            line-height: 14px
            font-size: 14px
            color: rgb(7, 17, 27)
          .desc
            font-size: 10px
            color: rgb(147, 153, 159)
            line-height: 12px
            margin: 8px 0
          .extra
            font-size: 10px
            color: rgb(147, 153, 159)
            line-height: 10px
            .rating
              margin-left: 12px
          .price
            font-size: 0
            .price-x, .price-y
              font-size: 10px
              font-weight: 700
              line-height: 24px
            .price-x
              color: red
              .price-xx
                font-size: 14px
            .price-y
              text-decoration: line-through
              color: rgb(147, 153, 159)
              margin-left: 8px
          .cartControl-wrapper
            position absolute
            right 0
            bottom 12px
</style>

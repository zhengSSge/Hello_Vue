<template>
  <div id="app">
    <v-header :seller="sellers"></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods" tag="a">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings" tag="a">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller" tag="a">商家</router-link>
      </div>
    </div>
    <keep-alive>
      <router-view :seller="sellers"></router-view>
    </keep-alive>
  </div>
</template>
<script>
    import axios from 'axios';
  import Header from './components/header/header';
  import {urlParse} from './common/js/util';
  //  import deta from '../data.json';

  const ERR_OK = 0;
  export default {
    data() {
      return {
        sellers: {
          id: (() => {
            let queryParam = urlParse();
            return queryParam.id;
          })()
        }
      };
    },
    created() {
//      本地请求不可置于git 该为直接获取 打包使用
//      this.sellers = deta.seller;
//      参数传递 本地请求不可置于git 该为直接获取
      let params = {
        'id': this.sellers.id
      };
//     2.0版本后 axios写法;
      axios.get('/api/seller', {params}).then((response) => {
        let responses = response.data;
        if (responses.errno === ERR_OK) {
//          this.sellers = aa.data;
          this.sellers = Object.assign({}, this.sellers, responses.data);
        }
      });
//      vue-resource 写法
//      this.$http.get('/api/seller?id=' + this.sellers.id).then(response => {
//        response = response.body;
//        if (response.errno === ERR_OK) {
//          this.sellers = response.data;
//          this.sellers = Object.assign({}, this.sellers, response.data);
//        }
//      }, response => {
//      });
    },
    components: {
      'v-header': Header
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "common/stylus/maxin.styl"
  .tab
    display: flex
    width: 100%
    height: 40px
    line-height: 40px
    border-1px(rgba(7, 17, 27, 0.1))
    .tab-item
      flex: 1
      text-align: center
      .tab-item > a
        display: block
        font-size: 14px
        color: rgb(77, 85, 93)

  .active
    color: rgb(240, 20, 20)
</style>

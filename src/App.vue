<template>
  <div>
    <my-header :sellermsg="seller"></my-header>
    <div class="tab">
      <router-link  :to="{ path: '/goods' }" tag="div" active-class="active" class="tab-item">商品</router-link>
      <router-link  :to="{ path: '/ratings' }" tag="div" active-class="active" class="tab-item">评论</router-link>
      <router-link  :to="{ path: '/seller' }" tag="div" active-class="active" class="tab-item">商家</router-link>
    </div>
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>

<script>
import MyHeader from './components/header/header';
export default {
  data () {
    return {
      seller: {}
    };
  },
  components: {
    MyHeader
  },
  created () {
    this.$http.get('/api/seller').then((res) => {
        let response = res.body;
        if (response.errno === 0) {
            this.seller = response.data;
        };
    });
  }
};
</script>

<style>
.tab{
  position:relative;
  display:flex;
  width:100%;
  height:40px;
  line-height:40px;
}
.tab:after{
  display:block;
  position:absolute;
  left:0;
  bottom:0;
  width:100%;
  border-top:1px solid rgba(7,17,27,.1);
  content:'';
}
.tab .tab-item{
  flex:1;
  text-align:center;
  font-size:14px;
  color:rgb(77,85,93);
}
.tab>div.active{
  color:rgb(240,20,20);
}
</style>

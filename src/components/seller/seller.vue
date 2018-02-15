<template>
    <div class="seller" ref="seller">
      <div class="seller-content">
        <div class="seller-header">
          <h1>{{seller.name}}</h1>
          <div class="seller-rate">
            <el-rate v-model="seller.score" disabled text-color="#ff9900" class="sellerrate"></el-rate>
            <div class="seller-count">({{seller.ratingCount}})</div>
            <div class="seller-sell">月售{{seller.sellCount}}单</div>
          </div>
          <ul class="remark">
            <li class="block">
              <h2>起送价</h2>
              <div class="block-content">
                <span class="stress">{{seller.minPrice}}</span>元
              </div>
            </li>
            <li class="block">
              <h2>商家配送</h2>
              <div class="block-content">
                <span class="stress">{{seller.deliveryPrice}}</span>元
              </div>
            </li>
            <li class="block">
              <h2>平均配送时间</h2>
              <div class="block-content">
                <span class="stress">{{seller.deliveryTime}}</span>分钟
              </div>
            </li>
          </ul>
          <div class="favorite" @click="toggleFavorite">
            <span class="iconfont icon-xihuanfill" :class="{'active':favorite}"></span>
            <span class="text">{{favoriteText}}</span>
          </div>
        </div>
        <div class="gonggao">
          <h1 class="gonggao-title">公告与活动</h1>
          <div class="gonggao-text">
              <p class="gonggao-content">{{seller.bulletin}}</p>
          </div>
          <ul class="details" v-if="seller.supports">
            <li class="support" v-for="(item,index) in seller.supports">
              <span class="icon"></span>
              <span class="text">{{item.description}}</span>
            </li>
          </ul>
        </div>
        <div class="pics">
          <h1 class="title">商家实景</h1>
          <div class="pic-wrapper" ref="picWrapper">
            <ul class="pic-list" ref="picList">
              <li class="pic-item" v-for="pic in seller.pics">
                <img :src="pic" width="120" height="90">
              </li>
            </ul>
          </div>
        </div>
        <div class="info">
          <h1 class="title border-1px">商家信息</h1>
          <ul>
            <li class="info-item" v-for="info in seller.infos">{{info}}</li>
          </ul>
        </div>
      </div>
    </div>
</template>

<script>
import BScroll from 'better-scroll';

export default {
  props: {
    seller: {
      type: Object,
      default: {}
    }
  },
  data () {
    return {
      favorite: 'false'
    };
  },
  computed: {
    favoriteText () {
      return this.favorite ? '已收藏' : '收藏';
    }
  },
  methods: {
    picscroll () {
      if (this.seller.pics) {
          let picWidth = 120;
          let margin = 6;
          let width = (picWidth + margin) * this.seller.pics.length - margin;
          this.$refs.picList.style.width = width + 'px';
          this.$nextTick(() => {
            if (!this.picScroll) {
              this.picScroll = new BScroll(this.$refs.picWrapper, {
                scrollX: true,
                eventPassthrough: 'vertical'
              });
            } else {
              this.picScroll.refresh();
            }
          });
        }
    },
    toggleFavorite () {
      this.favorite = !this.favorite;
    }
  },
  mounted () {
    if (!this.sellerScroll) {
      this.sellerScroll = new BScroll(this.$refs.seller, {
        click: true
      });
    } else {
      this.sellerScroll.refresh();
    }
    this.picscroll();
  }
};
</script>
<style>
.seller{
  position: absolute;
  top: 178px;
  bottom: 0;
  left: 0;
  width: 100%;
  overflow: hidden;
  background: #f3f5f7;
}
.seller-header{
  position: relative;
  padding:18px;
  background: white;
}
.seller-header h1{
  font-size: 14px;
  color:rgb(7,17,27);
  line-height: 14px;
  font-weight: 700;
  margin-bottom: 8px;
}
.seller-header .seller-count{
  display: inline-block;
  margin-left:8px;
  font-size: 10px;
  color:rgb(77,85,93);
  line-height: 18px;
  vertical-align: middle;
}
.seller-header .seller-sell{
  display: inline-block; 
  margin-left:12px;
  font-size: 10px;
  color:rgb(77,85,93);
  line-height: 18px;
  vertical-align: middle;   
}
.seller-header .sellerrate{
  display: inline-block;  
}
.seller-header .remark{
  display: flex;
  padding-top: 18px;
}
.seller-header .remark .block{
  flex: 1;
  text-align: center;
  border-right: 1px solid rgba(7, 17, 27, 0.1);
}
.seller-header .remark .block:last-child{
  border: none
}
.seller-header .remark h2{
  margin-bottom: 4px;
  line-height: 10px;
  font-size: 10px;
  color: rgb(147, 153, 159);
}
.seller-header .remark .block-content{
  line-height: 24px;
  font-size: 10px;
  color: rgb(7, 17, 27);
}
.seller-header .remark .stress{
  font-size: 24px;
}
.seller-header .favorite{
  position: absolute;
  width: 50px;
  right: 11px;
  top: 18px;
  text-align: center;
}
.seller-header .favorite .iconfont{
  display: block;
  margin-bottom: 4px;
  line-height: 24px;
  font-size: 24px;
  color: #d4d6d9;
}
.seller-header .favorite .active{
  color: rgb(240, 20, 20);
}
.seller-header .favorite .text{
  line-height: 10px;
  font-size: 10px;
  color: rgb(77, 85, 93);
}
.seller .gonggao{
  padding: 18px 18px 0 18px;
  background: white;
  margin-top:18px;
}
.gonggao .gonggao-title{
  margin-bottom: 8px;
  line-height: 14px;
  color: rgb(7, 17, 27);
  font-size: 14px;
}
.gonggao .gonggao-text{
  padding: 0 12px 16px 12px;
  border-bottom:1px solid rgba(7, 17, 27, 0.1);
}
.gonggao .gonggao-content{
  line-height: 24px;
  font-size: 12px;
  color: rgb(240, 20, 20);
}
.gonggao ul li.support{
    padding: 16px 12px;
    border-bottom:1px solid rgba(7, 17, 27, 0.1);
    font-size: 0;
}
.gonggao ul li.support:last-child{
    border-bottom: none;
}
.gonggao ul li .text{
    line-height: 16px;
    font-size: 12px;
    color: rgb(7, 17, 27);
}
.gonggao ul .icon{
    display: inline-block;
    width: 16px;
    height: 16px;
    vertical-align: top;
    margin-right: 6px;
    background-size: 16px 16px;
    background-repeat: no-repeat;
}
.gonggao ul li:nth-child(1) .icon{
    background-image: url(./decrease_4@2x.png);
}
.gonggao ul li:nth-child(2) .icon{
    background-image: url(./discount_4@2x.png);
}
.gonggao ul li:nth-child(3) .icon{
    background-image: url(./guarantee_4@2x.png);
}
.gonggao ul li:nth-child(4) .icon{
    background-image: url(./invoice_4@2x.png);
}
.gonggao ul li:nth-child(5) .icon{
    background-image: url(./special_4@2x.png);
}
.pics{
  padding: 18px;
  margin-top:18px;
  background: white;
}
.pics .title{
  margin-bottom: 12px;
  line-height: 14px;
  color: rgb(7, 17, 27);
  font-size: 14px;
}
.pics .pic-wrapper{
  width: 100%;
  overflow: hidden;
  white-space: nowrap;
}
.pics .pic-list{
  font-size: 0;
}
.pics .pic-item{
  display: inline-block;
  margin-right: 6px;
  width: 120px;
  height: 90px;
}
.pics .pic-item:last-child{
  margin: 0;
}
.info{
  padding: 18px 18px 0 18px;
  color: rgb(7, 17, 27);
  background: white;
  margin-top:18px;
}
.info .title{
  padding-bottom: 12px;
  line-height: 14px;
  border-bottom:1px solid rgba(7, 17, 27, 0.1);
  font-size: 14px;
}
.info .info-item{
  padding: 16px 12px;
  line-height: 16px;
  border-bottom:1px solid rgba(7, 17, 27, 0.1);
  font-size: 12px;
}
.info .info-item:last-child{
  border-bottom:none;
}
</style>
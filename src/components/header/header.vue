<template>
    <div class="header">
      <div class="content-wrapper">
        <div class="avatar">
            <img width="64" height="64" :src="sellermsg.avatar">
        </div>
        <div class="content">
            <div class="title">
                <span class="band"></span>
                <span class="text"> {{ sellermsg.name }} </span>
            </div>
            <div class="description">
                {{ sellermsg.description }}/{{ sellermsg.deliveryTime }}分钟送达
            </div>
            <div class="supports" v-if="sellermsg.supports">
                <span class="icon"></span>
                <span class="text"> {{ sellermsg.supports[0].description }} </span>
            </div>
        </div>
        <div class="moresupports" v-if="sellermsg.supports" @click="showDeatil">
            <span class="count"> {{ sellermsg.supports.length }}个</span>
            <i class="iconfont icon-jiantouyou"></i>
        </div>
      </div>
      <div class="buller-wrapper" @click="showDeatil">
        <div class="content">
            <i class="iconfont icon-gonggao1"></i>
            <div class="text"> {{ sellermsg.bulletin }} </div>
            <i class="iconfont icon-jiantouyou"></i>
        </div>
      </div>
      <div class="background">
        <img :src="sellermsg.avatar" width="100%" height="100%">
      </div>
      <div class="detail" v-show="detailShow">
            <div class="detail-wrapper clearfix">
                <div class="detail-main">
                    <h1 class="name">{{sellermsg.name}}</h1>
                    <el-rate v-model="sellermsg.score" disabled text-color="#ff9900" class="star"></el-rate>
                    <div class="title">
                        <div class="line"></div>
                        <div class="text">优惠信息</div>
                        <div class="line"></div>
                    </div>
                    <ul class="details" v-if="sellermsg.supports">
                        <li class="support" v-for="(item,index) in sellermsg.supports">
                            <span class="icon"></span>
                            <span class="text">{{item.description}}</span>
                        </li>
                    </ul>
                    <div class="title">
                        <div class="line"></div>
                        <div class="text">商家公告</div>
                        <div class="line"></div>
                    </div>
                    <div class="order">
                        <p> {{ sellermsg.bulletin }} </p>
                    </div>
                </div>
            </div>
            <div class="detail-close">
                <i class="iconfont icon-guanbi1" @click="close"></i>
            </div>
      </div>
    </div>
</template>

<script>
export default {
    props: {
        sellermsg: {
            type: Object,
            default: {}
        }
    },
    data () {
        return {
            detailShow: false
        };
    },
    methods: {
        showDeatil () {
            this.detailShow = true;
        },
        close () {
            this.detailShow = false;
        }
    }
};
</script>
<style>
.header{
    background:rgba(7,17,27,.5);
    color:#fff;
    width:100%;
    position:relative;
    overflow:hidden;
}
.content-wrapper{
    padding:24px 12px 18px 24px;
    font-size:0;
    position:relative;
}
.avatar{
    display: inline-block;
    border-radius: 2px;
}
.content {
    display: inline-block;
    margin-left:16px;
    font-size:14px;
}
.content .title {
    margin:2px 0 8px;
}
.content .title .band {
    display: inline-block;
    vertical-align: top;
    width: 30px;
    height: 18px;
    background-image: url(./brand@2x.png);
    background-size: 30px 18px;
    background-repeat:no-repeat;
}
.content .title .text {
    margin-left: 6px;
    vertical-align: top;
    font-size: 16px;
    font-weight: bold;
    line-height: 18px;
}
.content .description {
    margin:8px 0 10px;
    font-size: 12px;
    font-weight: 100;
    line-height: 12px;
}
.content .supports {
    margin:10px 0 2px;
}
.content .supports .icon{
    display: inline-block;
    vertical-align: top;
    margin-top:1px;
    width: 12px;
    height: 12px;
    background-image: url(./decrease_1@2x.png);
    background-size: 12px 12px;
    background-repeat: no-repeat;
}
.content .supports .text {
    display: inline-block;
    vertical-align: top;
    margin-left: 4px;
    font-size: 10px;
    font-weight: 100;
    line-height: 12px;
}
.moresupports {
    position:absolute;
    bottom:18px;
    right:12px;
    padding:0 8px;
    width:40px;
    height:24px;
    line-height:24px;
    border-radius:14px;
    background-color:rgba(0,0,0,.2);
    text-align:center;
}
.moresupports .count{
    font-size:10px;
}
.moresupports .icon-jiantouyou{
    font-size:8px;
    margin-left:5px;
}
.buller-wrapper{
    width:100%;
    height:28px;
    background-color:rgba(7,17,27,.2);
}
.buller-wrapper .content{
    width:100%;
    margin:0 12px;
    font-size:10px;
    height:28px;
    line-height:28px;
}
.buller-wrapper .content .iconfont{
    vertical-align: top;
    font-size:14px;
}
.buller-wrapper .content .icon-gonggao1{
    display:inline-block;
    margin-right:4px;
}
.buller-wrapper .content .text{
    width:80%;
    white-space: nowrap;
    overflow: hidden;
    text-overflow:ellipsis;
    display:inline-block;
}
.buller-wrapper .content .icon-jiantouyou{
    display:inline-block;
    margin-left:4px;
}
.background{
    position:absolute;
    top:0;
    left:0;
    width:100%;
    height:100%;
    z-index:-1;
    filter:blur(10px);
}
.detail{
    position:fixed;
    top:0;
    left:0;
    z-index:100;
    width:100%;
    height:100%;
    overflow:auto;
    background:rgba(7,17,27,.8);
}
.detail .clearfix{
    display:inline-block;
}
.detail .clearfix:after {
    content:"";
    line-height:0;
    height:0;
    clear:both;
    visibility:hidden;
}
.detail .detail-wrapper{
    min-height:100%;
    width:100%;
}
.detail .detail-wrapper .detail-main{
    margin-top:64px;
    padding-bottom:64px;
}
.detail-main .name{
    font-size:16px;
    font-weight:700;
    color:rgb(255,255,255);
    line-height:16px;
    text-align:center;
}
.detail-main .star{
    margin:16px auto 28px;
    line-height:24px;
    height:24px;
    text-align:center;
}
.detail-main .title{
    display:flex;
    width:80%;
    margin:30px auto 24px auto;
}
.detail-main .title .line{
    flex:1;
    position:relative;
    top:-6px;
    border-bottom:1px solid rgba(255,255,255,0.2);
}
.detail-main .title .text{
    padding:0 12px;
    font-size:14px;
}
.detail-main ul.details{
    display:block;
    width:70%;
    margin:24px auto 28px auto;
}
.detail-main ul li.support{
    margin-bottom:12px;
}
.detail-main ul li .text{
    font-size:12px;
    font-weight:200;
    color:rgba(255,255,255);
    line-height:12px;
}
.detail-main ul .icon{
    display:inline-block;
    width:16px;
    height:16px;
    margin-right:6px;
    margin-top:1px;
    vertical-align: top;
    background-size: 16px 16px;
    background-repeat: no-repeat;
}
.detail-main ul li:nth-child(1) .icon{
    background-image: url(./decrease_1@2x.png);
}
.detail-main ul li:nth-child(2) .icon{
    background-image: url(./discount_1@2x.png);
}
.detail-main ul li:nth-child(3) .icon{
    background-image: url(./guarantee_1@2x.png);
}
.detail-main ul li:nth-child(4) .icon{
    background-image: url(./invoice_1@2x.png);
}
.detail-main ul li:nth-child(5) .icon{
    background-image: url(./special_1@2x.png);
}
.detail-main .order{
    width:70%;
    margin:24px auto 0;
}
.detail-main .order p{
    font-size:12px;
    font-weight:200;
    color:rgb(255,255,255);
    line-height:24px;
}
.detail .detail-close{
    position:relative;
    width:32px;
    height:32px;
    margin:-64px auto 0;
    clear:both;
    font-size: 32px;
}
</style>
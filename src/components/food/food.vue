<template>
    <transition name="move">
        <div class="food" v-show="showFlag" ref="foodpage">
            <div>
            <div class="pictrue">
                <img :src="food.image" alt="">
                <div @click="hide">
                    <i class="el-icon-back back"></i>
                </div>
            </div>
            <div class="food-content">
                <h1 class="food-name"> {{food.name}} </h1>
                <div class="food-detail">
                    <span class="food-sell">月售{{food.sellCount}}份</span>
                    <span class="food-rating">好评率{{food.rating}}%</span>
                </div>
                <div class="food-price">
                    <span class="price"> ￥{{ food.price }} </span>
                    <span class="oldprice" v-if="food.oldPrice"> ￥{{ food.oldPrice }} </span>
                    <div class="button" v-show="food.count>0">
                        <i class="el-icon-remove icon" v-show="food.count>0" @click.stop="delatemyfood(food,$event)"></i>
                        <span v-show="food.count>0" class="count">{{food.count}}</span>
                        <i class="el-icon-circle-plus icon" @click.stop="addmyfood(food,$event)"></i>
                    </div>
                    <div class="buy" v-show="!food.count || food.count===0" @click.stop="addmyfood(food,$event)">
                        <span class="gobuy">加入购物车</span>
                    </div>
                </div>
            </div>
            <div class="food-info">
                <h1 class="title">商品介绍</h1>
                <div class="text">
                    <span>{{food.info}}</span>
                </div>
            </div>
            <div class="food-ratings">
                <h1 class="title">商品评价</h1>
                <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings" 
                @changerating="changeRating" @changeonlyread="changeOnlyRead"></ratingselect>
                <div class="rating-wrapper">
                    <ul v-show="food.ratings && food.ratings.length">
                    <li v-for="rating in food.ratings" class="rating-item" :key="rating.username" v-show="needShow(rating.rateType, rating.text)">
                        <div class="user">
                            <span class="name">{{rating.username}}</span>
                            <img :src="rating.avatar" alt="" class="avatar" width="12" height="12">
                        </div>
                        <div class="time">{{rating.rateTime | formatDate}}</div>
                        <p class="text">
                                <span :class="{'icon-zantongfill iconfont':rating.rateType===0,'icon-zan1 iconfont':rating.rateType===1}"></span>
                                <span>{{rating.text}}</span>
                        </p>
                    </li>
                    </ul>
                    <div class="no-ratings" v-show="!food.ratings || !food.ratings.length"></div>
                </div>       
            </div>
            </div>
        </div>
    </transition>
</template>
<script scoped>
import BScroll from 'better-scroll';
import ratingselect from '../ratingselect/ratingselect';
import {formatDate} from '../../common/js/date';
// import Vue from 'vue';
// const POSITIVE = 0;
// const NEGATIVE = 1;
const ALL = 2;
    export default {
        props: {
            food: {
                type: Object
            }
        },
        data () {
            return {
                showFlag: false,
                selectType: ALL,
                onlyContent: true,
                desc: {
                    all: '全部',
                    positive: '推荐',
                    negative: '吐槽'
                }
            };
        },
        components: {
            ratingselect
        },
        methods: {
            show () {
                this.showFlag = true;
                this.selectType = ALL;
                this.onlyContent = false;
                this.$nextTick(() => {
                    if (!this.myfoodscroll) {
                        this.myfoodscroll = new BScroll(this.$refs.foodpage, {
                            click: true
                        });
                    } else {
                        this.myfoodscroll.refresh();
                    }
                });
            },
            hide () {
                this.showFlag = false;
            },
            addmyfood (food, event) {
                if (!event._constructed) {
                    return;
                }
                this.$emit('choose', food, event);
            },
            delatemyfood (food, event) {
                if (!event._constructed) {
                    return;
                }
                this.$emit('delatemy', food, event);
            },
            needShow (type, text) {
                if (this.onlyContent && !text) {
                    return false;
                }
                if (this.selectType === ALL) {
                    return true;
                } else {
                    return type === this.selectType;
                }
            },
            changeRating (type, event) {
                this.selectType = type;
                this.$nextTick(() => {
                    this.myfoodscroll.refresh();
                });
            },
            changeOnlyRead () {
                this.onlyContent = !this.onlyContent;
                this.$nextTick(() => {
                    this.myfoodscroll.refresh();
                });
            }
        },
        filters: {
            formatDate (time) {
                let date = new Date(time);
                return formatDate(date, 'yyyy-MM-dd hh:mm');
            }
        }
    };
</script>
<style>
.food{
    position: fixed;
    left:0;
    top:0;
    bottom:48px;
    z-index: 30;
    width:100%;
    background:#f3f5f7;
}
.move-enter-active{
    transition: all .3s linear;
    transform: translateX(0);    
}
.move-enter{
    transform: translateX(100%);
}
.move-leave-active{
    transition: all .3s linear;
    transform: translateX(100%);    
}
.move-leave{
    transform: translateX(0);
}
.pictrue{
    position: relative;
    width:100%;
    height:0;
    padding-top:100%;
}
.pictrue img{
    position:absolute;
    top:0;
    left:0;
    width:100%;
    height:100%;
}
.pictrue>div{
    position: absolute;
    top:10px;
    left:0;
}
.pictrue .back{
    display: block;
    padding: 10px;
    font-size:20px;
    color:black;
}
.food-content{
    position: relative;
    padding:18px;
    background: white;
    border-bottom: 1px solid rgba(7,17,27,.1);
    margin-bottom: 16px;
}
.food-content .food-name{
    font-size: 14px;
    font-weight: 700;
    color:rgb(7,17,27);
    line-height: 14px;
    margin-bottom: 8px;
}
.food-content .food-detail{
    margin-bottom: 18px;
    line-height: 10px;
    font-size: 10px;
    color:rgb(147,153,159);
}
.food-content .food-detail .food-rating{
    margin-left: 12px;
}
.food-content .food-price{
    position: relative;
    line-height: 24px;
}
.food-content .food-price .price{
    font-size: 14px;
    font-weight: 700;
    color:rgb(240,20,20);
}
.food-content .food-price .oldprice{
    font-size: 10px;
    font-weight: normal;
    color:rgb(147,153,159);
    text-decoration: line-through;
    margin-left: 12px;
}
.food-content .food-price .button{
    display: inline-block;
    position: absolute;
    right:0;
    top:0;
    line-height: 24px;
}
.food-content .food-price .button .count{
    font-size:10px;
    color:rgb(147,153,159);
    margin:0 5px;
    text-align: center;
}
.food-content .food-price .button .icon{
    font-size:24px;
    color:rgb(0,160,220);
    vertical-align: top;
}
.food-content .food-price .buy{
    display: inline-block;
    position: absolute;
    right:0;
    top:0;
    width:74px;
    height:24px;
    background-color:rgb(0,160,220);
    border-radius: 12px;
    color:rgb(255,255,255);
    text-align: center;
    vertical-align: top;
}
.food-content .food-price .buy .gobuy{
    font-size: 10px;
    line-height: 12px;
}
.food-info{
    padding:18px;
    background: white;
    border-bottom: 1px solid rgba(7,17,27,.1);
    border-top: 1px solid rgba(7,17,27,.1);
    margin-bottom: 16px;    
}
.food-info .title{
    margin-bottom: 6px;
}
.food-info .text{
    margin:0 8px;
    font-size: 12px;
    font-weight: 200;
    color:rgb(77,85,93);
    line-height: 24px;
}
.food-ratings{
    border-bottom: 1px solid rgba(7,17,27,.1);
    border-top: 1px solid rgba(7,17,27,.1);
    background: white;
    padding:18px;
}
.food-ratings .title{
    margin-bottom: 6px;
}
.rating-wrapper .rating-item{
    position: relative;
    padding: 16px 0;
    border-bottom:1px solid rgba(7, 17, 27, 0.1);
}
.rating-wrapper .rating-item .user{
    position: absolute;
    right: 0;
    top: 16px;
    line-height: 12px;
    font-size: 0;
}
.rating-wrapper .rating-item .user .name{
    display: inline-block;
    margin-right: 6px;
    vertical-align: top;
    font-size: 10px;
    color: rgb(147, 153, 159);
}
.rating-wrapper .rating-item .user .avatar{
    border-radius: 50%
}
.time{
    margin-bottom: 6px;
    line-height: 12px;
    font-size: 10px;
    color: rgb(147, 153, 159);
}
.rating-wrapper .rating-item .text{
    line-height: 16px;
    font-size: 12px;
    color: rgb(7, 17, 27);
}
.rating-wrapper .rating-item .iconfont{
    margin-right: 4px;
    line-height: 16px;
    font-size: 12px;
}
.rating-wrapper .rating-item .icon-zantongfill{
    color: rgb(0, 160, 220);
}
.rating-wrapper .rating-item .icon-zan1{
    color: rgb(147, 153, 159);
}
.food-ratings .no-ratings{
    font-size: 12px;
    color: rgb(147, 153, 159);
}
</style>

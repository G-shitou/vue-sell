<template>
<div>
    <div class="shopcart">
        <div class="content" @click="togglelist">
            <div class="content-left">
                <div class="logo-wrapper">
                    <div class="logo" :class="{'light':totalCount>0}">
                        <span class="iconfont icon-gouwuchefill" :class="{'light':totalCount>0}"></span>
                    </div>
                    <div class="num" v-show="totalCount>0">{{totalCount}}</div>
                </div>
                <div class="price">￥{{totalPrice}}</div>
                <div class="desc">另需配送费￥{{price}}元</div>
            </div>
            <div class="content-right">
                <div class="pay" :class="{'light':totalPrice>=minPrice}" @click.stop.prevent="pay">
                    {{showPay}}
                </div>
            </div>
        </div>
        <div class="ball-container">
                <transition name="fade" v-for="ball in balls" :key="ball.id" @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
                    <div class="ball" v-show="ball.show">
                        <div class="inner inner-hook"></div>
                    </div>
                </transition>
        </div>
        <transition tag="div" name="fold">
            <div v-show="listShow" class="shopcart-list">
            <div class="list-header">
                <h1 class="title">购物车</h1>
                <span class="empty" @click="empty">清空</span>
            </div>
            <div class="list-content" ref="listContent">
                <ul>
                    <li class="food" v-for="food in selectFoods" :key="food.name">
                        <span class="name">{{food.name}}</span>
                        <div class="price">
                            <span>￥{{food.price*food.count}}</span>
                        </div>
                        <div class="buttoned">
                            <i class="el-icon-remove icon" @click="delateFood(food,$event)"></i>
                            <span class="count">{{food.count}}</span>
                            <i class="el-icon-circle-plus icon"  @click="addFood(food,$event)"></i>
                        </div>
                    </li>
                </ul>
            </div>
            </div>
        </transition>    
    </div>
    <transition name="fade">
            <div class="list-mask" @click="hideList" v-show="listShow"></div>
    </transition>
</div> 
</template>
<script>
import BScroll from 'better-scroll';
import Vue from 'vue';
    export default {
        props: {
            price: {
                type: Number,
                default: 0
            },
            minPrice: {
                type: Number,
                default: 0
            },
            selectFoods: {
                type: Array,
                default () {
                    return [];
                }
            }
        },
        data () {
            return {
                balls: [
                    {
                        show: false,
                        id: 0
                    },
                    {
                        show: false,
                        id: 1
                    },
                    {
                        show: false,
                        id: 2
                    },
                    {
                        show: false,
                        id: 3
                    },
                    {
                        show: false,
                        id: 4
                    }
                ],
                dropBalls: [],
                fold: true
            };
        },
        computed: {
            // 计算总计价格
            totalPrice () {
                let total = 0;
                this.selectFoods.forEach((food) => {
                    total += food.count * food.price;
                });
                return total;
            },
            // 计算商品个数，如果大于0，则购物车改变样式
            totalCount () {
                let count = 0;
                this.selectFoods.forEach((food) => {
                    count += food.count;
                });
                return count;
            },
            // 结算描述,三种情况。
            showPay () {
                if (this.totalPrice === 0) {
                    return `￥${this.minPrice}起送`;
                } else if (this.totalPrice < this.minPrice) {
                    let price = this.minPrice - this.totalPrice;
                    return `还差￥${price}元`;
                } else {
                    return `去结算`;
                }
            },
            listShow () {
                if (!this.totalCount) {
                    this.fold = true;
                    return false;
                }
                let show = !this.fold;
                if (show) {
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
            drop (el) {
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
            beforeEnter (el) {
                let count = this.balls.length;
                while (count--) {
                    let ball = this.balls[count];
                    if (ball.show) {
                        let rect = ball.el.getBoundingClientRect();
                        let x = rect.left - 32;
                        let y = -(window.innerHeight - rect.top - 22);
                        el.style.display = '';
                        el.style.webkitTransform = `translate3d(0,${y}px,0)`;
                        el.style.transform = `translate3d(0,${y}px,0)`;
                        let inner = el.getElementsByClassName('inner-hook')[0];
                        inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
                        inner.style.transform = `translate3d(${x}px,0,0)`;
                    }
                }
            },
            enter (el, done) {
                /* eslint-disable no-unused-vars */
                let rf = el.offsetHeight;
                this.$nextTick(() => {
                    el.style.webkitTransform = 'translate3d(0,0,0)';
                    el.style.transform = 'translate3d(0,0,0)';
                    let inner = el.getElementsByClassName('inner-hook')[0];
                    inner.style.webkitTransform = 'translate3d(0,0,0)';
                    inner.style.transform = 'translate3d(0,0,0)';
                    el.addEventListener('transitionend', done);
                });
            },
            afterEnter (el) {
                let ball = this.dropBalls.shift();
                if (ball) {
                    ball.show = false;
                    el.style.display = 'none';
                }
            },
            togglelist () {
                if (!this.totalCount) {
                    return;
                }
                this.fold = !this.fold;
            },
            addFood (food, event) {
                if (!event._constructed) {
                    return;
                }
                if (!food.count) {
                    Vue.set(food, 'count', 1);
                } else {
                    food.count++;
                }
            },
            delateFood (food, event) {
                if (!event._constructed) {
                    return;
                }
                if (food.count) {
                    food.count--;
                }
            },
            empty () {
                this.selectFoods.forEach((food) => {
                    food.count = 0;
                });
            },
            hideList () {
                this.fold = true;
            },
            pay () {
                if (this.totalPrice < this.minPrice) {
                    return;
                }
                window.alert(`支付${this.totalPrice}元`);
                this.empty();
            }
        }
    };
</script>
<style>
.shopcart{
    position: fixed;
    left:0;
    bottom:0;
    z-index:50;
    width:100%;
    height:48px;
}
.shopcart .content{
    display: flex;
    margin-left:0;
    background: #141d27;
    font-size:0;
}
.shopcart .content-left{
    flex:1;
}
.shopcart .content-left .logo-wrapper{
    display: inline-block;
    position: relative;
    top:-10px;
    margin: 0 12px;
    padding:6px;
    width:56px;
    height:56px;
    box-sizing: border-box;
    vertical-align: top;
    border-radius: 50%;
    background: #141d27;

}
.shopcart .content-left .logo-wrapper .num{
    position: absolute;
    top:0;
    right:0;
    height:16px;
    width:24px;
    line-height: 16px;
    text-align: center;
    border-radius: 16px;
    font-size:9px;
    font-weight:700;
    color:white;
    background:rgb(240,20,20);
    box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4);
}
.shopcart .content-left .logo-wrapper .logo{
    width:100%;
    height:100%;
    border-radius: 50%;
    background: #2b343c;
    text-align: center;
}
.shopcart .content-left .logo-wrapper div.light{
    background: rgb(0,160,220);
}
.shopcart .content-left .logo-wrapper .logo .iconfont{
    font-size:24px;
    color:rgba(255,255,255,0.4);
    line-height: 44px;
}
.shopcart .content-left .logo-wrapper .logo .light{
    color:#fff;
}
.shopcart .content-left .price{
    display: inline-block;
    vertical-align: top;
    margin-top:12px;
    line-height: 24px;
    padding-right:12px;
    box-sizing:border-box;
    border-right: 1px solid rgba(255,255,255,0.1);
    font-size:16px;
    font-weight:700;
    color:rgba(255,255,255,0.4);

}
.shopcart .content-left .desc{
    display: inline-block;
    vertical-align: top;
    line-height: 24px;
    margin:12px 0 0 12px;
    line-height: 24px;
    color:rgba(255,255,255,0.4);
    font-size:10px;
}
.shopcart .content-right{
    flex:0 0 105px;
    width:105px;
}
.shopcart .content-right .pay{
    height:48px;
    line-height: 48px;
    text-align: center;
    font-size: 12px;
    color:rgba(255,255,255,0.4);
    font-weight:700;
    background:#2b333b;
}
.shopcart .content-right .light{
    background: #00b43c;
    color:#fff;
}
.shopcart .ball-container .ball{
    position: fixed;
    left:32px;
    bottom:22px;
    z-index:200;
    transition: all .4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
}
.shopcart .ball-container .inner{
    width:16px;
    height:16px;
    border-radius: 50%;
    background: rgb(0,160,220);
    transition: all .4s linear;
}
.shopcart .shopcart-list{
    position: absolute;
    bottom:48px;
    left:0;
    z-index:-1;
    width:100%;
}
.fold-enter-active,.fold-leave-active{    
    transition: all .5s;    
}
.fold-enter-active,.fold-leave{   
    transform: translateY(0);
}
.fold-enter,.fold-leave-active{
    transform: translateY(100%);
}
.shopcart .shopcart-list .list-header{
    height: 40px;
    line-height: 40px;
    padding: 0 18px;
    background: #f3f5f7;
    border-bottom: 1px solid rgba(7, 17, 27, 0.3);
}
.shopcart .shopcart-list .list-header .title{
    float: left;
    font-size: 14px;
    color: rgb(7, 17, 27);
}
.shopcart .shopcart-list .list-header .empty{
    float: right;
    font-size: 12px;
    color: rgb(0, 160, 220);
}
.shopcart .list-content{
    padding: 0 18px;
    max-height: 217px;
    overflow: hidden;
    background: #fff;
}
.shopcart .list-content .food{
    position: relative;
    padding: 12px 0;
    box-sizing: border-box;
    border-bottom:1px solid rgba(7, 17, 27, 0.1);
    background: white;
}
.shopcart .list-content .name{
    line-height: 24px;
    font-size: 14px;
    color: rgb(7, 17, 27);
}
.shopcart .list-content .price{
    position: absolute;
    right: 90px;
    bottom: 12px;
    line-height: 24px;
    font-size: 14px;
    font-weight: 700;
    color: rgb(240, 20, 20);
}
.shopcart .list-content .buttoned{
    position: absolute;
    right: 0;
    bottom: 12px;
}
.shopcart .list-content .buttoned .count{
    font-size:10px;
    color:rgb(147,153,159);
    margin:0 5px;
    text-align: center;
}
.shopcart .list-content .buttoned .icon{
    font-size:24px;
    color:rgb(0,160,220);
    vertical-align: top;
}
.list-mask{
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 40;
    background:  rgb(7, 17, 27);
    opacity: 0.6;
}


</style>


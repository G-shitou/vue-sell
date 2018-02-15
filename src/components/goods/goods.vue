<template>
    <div class="goods">
        <div class="menu-wrapper" ref="menuWrapper">
            <ul>
                <li v-for="(item,index) in goods" :key="item.name" class="menu-item" 
                :class="{'current':currentIndex===index}" @click="select(index,$event)">
                    <span v-show="item.type" class="icon" :class="classMap[item.type]"></span>
                    <span class="text">{{item.name}}</span>
                </li>
            </ul>
        </div>
        <div class="foods-wrapper" ref="foodsWrapper">
            <ul>
                <li v-for="item in goods" :key="item.name" class="foodlist">
                    <h1> {{ item.name }} </h1>
                    <div class="foods" v-for="food in item.foods" :key="food.name" @click="selectFood(food,$event)">
                            <div class="picture">
                                <img :src="food.icon" >
                            </div>
                            <div class="text">
                                <p class="name"> {{ food.name }} </p>
                                <p class="type"> {{ food.description }} </p>
                                <div class="num">
                                    <span class="count"> 月售 {{ food.sellCount }} 份</span>
                                    <span class="baifen">好评率 {{ food.rating }}% </span>
                                </div>
                                <div class="Price">
                                    <span class="price"> ￥{{ food.price }} </span>
                                    <span class="oldprice" v-if="food.oldPrice"> ￥{{ food.oldPrice }} </span>
                                    <div class="button">
                                        <i class="el-icon-remove icon" v-show="food.count>0" @click.stop="delateFood(food,$event)"></i>
                                        <span v-show="food.count>0" class="count">{{food.count}}</span>
                                        <i class="el-icon-circle-plus icon"  @click.stop="addFood(food,$event)"></i>
                                    </div>
                                </div>
                            </div> 
                    </div>
                </li>
            </ul>
        </div>
        <ShopCars ref="shopcars" :select-foods="selectFoods" :price="seller.deliveryPrice" :min-price="seller.minPrice"></ShopCars>
        <food :food="selectedFood" ref="food" @choose="addFood" @delatemy="delateFood"></food>
    </div>
</template>

<script>
import Vue from 'vue';
import Bscroll from 'better-scroll';
import ShopCars from '../shopcars/shopcars';
import food from '../food/food';
export default {
    props: {
        seller: {
            type: Object
        }
    },
    data () {
        return {
            goods: [],
            listHeight: [],
            scrollY: 0,
            buynum: 0,
            selectedFood: {}
        };
    },
    components: {
        ShopCars,
        food
    },
    computed: {
        currentIndex () {
            // 右侧滚动的时候对左侧index的判断，相应index更改样式，完成右对左的滚动映射。
            for (let i = 0; i < this.listHeight.length; i++) {
                let height1 = this.listHeight[i];
                let height2 = this.listHeight[i + 1];
                if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
                    return i;
                };
            };
            return 0;
        },
        // 遍历food，如果food有count属性，则代表选中过该商品，把选中的商品的数组传给购物车，实现联动
        selectFoods () {
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
    mounted () {
        this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
        this.$http.get('./api/goods').then((res) => {
            let response = res.body;
            if (response.errno === 0) {
                this.goods = response.data;
                this.$nextTick(() => {
                    if (!this.menuScroll || !this.foodsScroll) {
                        this.calculateHeight();
                        this.Scroll();
                    } else {
                        this.menuScroll.refresh();
                        this.foodsScroll.refresh();
                    }
                });
            };
        });
    },
    methods: {
        Scroll () {
            this.menuScroll = new Bscroll(this.$refs.menuWrapper, {click: true});
            this.foodsScroll = new Bscroll(this.$refs.foodsWrapper, {click: true, probeType: 3});
            this.foodsScroll.on('scroll', (pos) => {
                this.scrollY = Math.abs(Math.round(pos.y));
            });
        },
        calculateHeight () {
            // 对height初始化加载
            let foodlist = this.$refs.foodsWrapper.getElementsByClassName('foodlist');
            let height = 0;
            this.listHeight.push(height);
            for (let i = 0; i < foodlist.length; i++) {
                let item = foodlist[i];
                height += item.clientHeight;
                this.listHeight.push(height);
            };
        },
        select (index, evnet) {
            // 判断是否为pc端，pc端不做处理，以免触发两次点击事件
            if (!event._constructed) {
                return;
            };
            let foodlist = this.$refs.foodsWrapper.getElementsByClassName('foodlist');
            let el = foodlist[index];
            this.foodsScroll.scrollToElement(el, 300);
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
            this._drop(event.target);
        },
        delateFood (food, event) {
            if (!event._constructed) {
                return;
            }
            if (food.count) {
                food.count--;
            }
        },
        _drop (target) {
            this.$refs.shopcars.drop(target);
        },
        selectFood (food, event) {
            // 判断是否为pc端，pc端不做处理，以免触发两次点击事件
            if (!event._constructed) {
                return;
            };
            this.selectedFood = food;
            this.$refs.food.show();
        }
    }
};
</script>
<style>
.goods{
    display:flex;
    position:absolute;
    top:178px;
    bottom:46px;
    width:100%;
    overflow:hidden;
}
.menu-wrapper{
    flex:0 0 80px;
    width:80px;
    background:#f3f5f7;
}
.menu-wrapper .menu-item{
    display:table;
    width:56px;
    height:54px;
    line-height:14px;
    font-size:12px;
    font-weight:200;
    padding:0 12px;
}
.menu-wrapper .current{
    position: relative;
    z-index: 10;
    background-color:rgba(0,0,0,.15);
    font-weight:700;
}
.menu-wrapper .menu-item .text{
    display:table-cell;
    vertical-align:middle;
    text-align:center;
}
.menu-wrapper .menu-item .icon{
    display:inline-block;
    width:14px;
    height:14px;
    margin-right:2px;
    margin-top:14px;
    vertical-align: middle;
    background-size: 14px 14px;
    background-repeat: no-repeat;
}
.menu-wrapper .menu-item .decrease{
    background-image: url(../header/decrease_1@2x.png);
}
.menu-wrapper .menu-item .discount{
    background-image: url(../header/discount_1@2x.png);
}
.menu-wrapper .menu-item .guarantee{
    background-image: url(../header/guarantee_1@2x.png);
}
.menu-wrapper .menu-item .invoice{
    background-image: url(../header/invoice_1@2x.png);
}
.menu-wrapper .menu-item .special{
    background-image: url(../header/special_1@2x.png);
}
.foods-wrapper{
    flex:1;
}
.foods-wrapper .foodlist{
    display: block;
}
.foods-wrapper .foodlist h1{
    height:26px;
    font-size: 12px;
    line-height: 26px;
    color: rgb(147,153,159);
    background-color: #f3f5f7;
    padding-left:14px;
}
.foods-wrapper .foodlist .foods{
    display: flex;
    background-color: white;
    margin:18px;    
}
.foods-wrapper .foodlist .foods .picture{
    flex:0,0,57px;
    width:60px;
    height:60px;
}
.foods-wrapper .foodlist img{
    width:100%;
    height:100%;
}
.foods-wrapper .foodlist .text{
    flex:1;
    margin-left:10px;
    margin-top:2px;
}
.foods-wrapper .foodlist .text .name{
    font-size:14px;
    color:rgb(7,17,27);
    line-height: 14px;
    margin-bottom:8px;
}
.foods-wrapper .foodlist .text .type{
    font-size:10px;
    color:rgb(147,153,159);
    line-height: 12px;
    margin-bottom: 8px;
}
.foods-wrapper .foodlist .text .num{
    font-size:10px;
    color:rgb(147,153,159);
    line-height: 10px;
    margin-bottom: 8px;
}
.foods-wrapper .foodlist .text .num .baifen{
    margin-left:12px;
}
.foods-wrapper .foodlist .text .Price{
    position: relative;
    width:100%;
}
.foods-wrapper .foodlist .text .Price .price{
    font-size: 14px;
    color:red;
    font-weight:700;
    line-height:24px;
}
.foods-wrapper .foodlist .text .Price .oldprice{
    font-size: 10px;
    color:rgb(147,153,159);
    line-height:24px;
    text-decoration:line-through;
    margin-left:8px;
}
.foods-wrapper .foodlist .text .Price .button{
    display: inline-block;
    position: absolute;
    right:0;
    top:0;
    line-height: 24px;
}
.foods-wrapper .foodlist .text .Price .button .count{
    font-size:10px;
    color:rgb(147,153,159);
    margin:0 5px;
    text-align: center;
}
.foods-wrapper .foodlist .text .Price .button .icon{
    font-size:24px;
    color:rgb(0,160,220);
    vertical-align: top;
}
</style>
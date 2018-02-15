<template>
    <div class="ratingselect">
        <div class="rating-type">
            <span class="positive block" :class="{'active':selectType===2}" @click="select(2,$event)">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
            <span class="positive block" :class="{'active':selectType===0}" @click="select(0,$event)">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
            <span class="negative block" :class="{'actived':selectType===1}" @click="select(1,$event)">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
        </div>
        <div class="switch" :class="{'on':onlyContent}" @click="toggleContent">
            <span class="el-icon-success icon"></span>
            <span class="text">只看有内容的评价</span>
        </div>
    </div>
</template>
<script>
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;

export default {
    props: {
        ratings: {
            type: Array,
            default () {
                return [];
            }
        },
        selectType: {
            type: Number,
            default: ALL
        },
        onlyContent: {
            type: Boolean,
            default: false
        },
        desc: {
            type: Object,
            default () {
                return {
                    all: '全部',
                    positive: '满意',
                    negative: '不满意'
                };
            }
        }
    },
    methods: {
        select (type, event) {
            if (!event._constructed) {
                return;
            }
            this.$emit('changerating', type, event);
        },
        toggleContent () {
            if (!event._constructed) {
                return;
            }
            this.$emit('changeonlyread');
        }
    },
    computed: {
        // 计算好的评论
        positives () {
            let num = [];
            this.ratings.forEach((rating) => {
                if (rating.rateType === POSITIVE) {
                   num.push(rating);
                }
            });
            return num;
        },
        // 计算不好的评论
        negatives () {
            let num = [];
            this.ratings.forEach((rating) => {
                if (rating.rateType === NEGATIVE) {
                   num.push(rating);
                }
            });
            return num;
        }
    }
};
</script>
<style>
.ratingselect .rating-type{
    padding:12px 0 18px;
    border-bottom:1px solid rgba(7,17,27,0.1);
}
.ratingselect .rating-type .block{
    display: inline-block;
    padding:8px 12px;
    line-height: 16px;
    font-size: 12px;
    border-radius: 1px;
    margin-left:8px;
}
.ratingselect .rating-type .positive{
    background-color: rgba(0,160,220,.2);    
    color:rgb(77,85,93);
}
.ratingselect .rating-type .negative{
    background:rgba(77,85,93,.2);
}
.ratingselect .rating-type .count{
    display: inline-block;
    font-size: 8px;
}
.ratingselect .rating-type>span:first-child{
    margin-left:0;    
}
.ratingselect .rating-type .active{
    background: rgb(0,160,240);
    color:white;
    border-radius: 12px;
}
.ratingselect .rating-type .actived{
    background:rgb(77,85,93);
    color:white;
    border-radius: 12px;
}
.ratingselect .switch{
    padding:12px 0;
    border-bottom:1px solid rgba(7,17,27,0.1);
    line-height: 24px;
    color:rgb(147,153,159);   
}
.ratingselect .switch .icon{
    font-size: 24px;
}
.ratingselect .switch .text{
    font-size: 12px;
    vertical-align: top;
    margin-left:4px;
}
.ratingselect .on .icon{
    color:#00c850;
}
</style>

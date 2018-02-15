<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="ratings-score">
        <div class="ratings-leftscore">
          <p class="score">{{seller.score}}</p>
          <h1 class="title">综合评分</h1>
          <p class="text">高于周边商家{{seller.rankRate}}%</p>
        </div>
        <div class="ratings-rightscore">
          <div class="boxcenter">
          <div class="score">
            <div class="title">服务态度</div>
            <div class="starbox">
              <el-rate v-model="seller.serviceScore" disabled show-score text-color="#ff9900" class="star"></el-rate>
            </div>
          </div>
          <div class="score">
            <div class="title">商品评价</div>
            <div class="starbox">
              <el-rate v-model="seller.foodScore" disabled show-score text-color="#ff9900" class="star"></el-rate>
            </div>
          </div>
          <div class="score">
            <div class="title">送达时间</div>
            <div class="score-time">{{seller.deliveryTime}}分钟</div>
          </div>
          </div>
        </div>
      </div>
      <ratingselect class="ratingselect" :select-type="selectType" :only-content="onlyContent" :ratings="ratings" 
                @changerating="changeRating" @changeonlyread="changeOnlyRead"></ratingselect>
      <div class="rating-wrapper">
        <ul>
          <li v-for="rating in ratings" class="rating-item" v-show="needShow(rating.rateType, rating.text)">
            <div class="avatar">
              <img width="28" height="28" :src="rating.avatar">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <el-rate v-model="rating.score" disabled-void-color="" disabled show-score  text-color="#ff9900" class="star"></el-rate>
              <p class="text">{{rating.text}}</p>
              <div class="recommend">
                <span :class="{'icon-zantongfill iconfont':rating.rateType===0,'icon-zan1 iconfont':rating.rateType===1}"></span>
                <span class="item" v-for="item in rating.recommend" v-show="rating.recommend && rating.recommend.length">{{item}}</span>
              </div>
              <div class="mytime">
                {{rating.rateTime | formatDate}}
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import ratingselect from '../ratingselect/ratingselect';
import {formatDate} from '../../common/js/date';
import BScroll from 'better-scroll';
const ALL = 2;
export default {
  props: {
    seller: {
      type: Object,
      default: {}
    }
  },
  data () {
    return {
      ratings: [],
      selectType: ALL,
      onlyContent: false
    };
  },
  components: {
    ratingselect
  },
  created () {
    this.$http.get('/api/ratings').then((response) => {
        response = response.body;
        if (response.errno === 0) {
          this.ratings = response.data;
          this.$nextTick(() => {
            this.scroll = new BScroll(this.$refs.ratings, {
              click: true
            });
          });
        }
    });
  },
  filters: {
    formatDate (time) {
      let date = new Date(time);
      return formatDate(date, 'yyyy-MM-dd hh:mm');
    }
  },
  methods: {
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
        this.scroll.refresh();
      });
    },
    changeOnlyRead () {
      this.onlyContent = !this.onlyContent;
      this.$nextTick(() => {
        this.scroll.refresh();
      });
    }
  }
};
</script>
<style>
.ratings{
  position:absolute;
  top:178px;
  bottom:0px;
  left:0;
  width:100%;
  overflow: hidden;
  background: #f3f5f7;
}
.ratings-score{
  display: flex;
  padding:18px 0;
  background: white;
}
.ratings-leftscore{
  flex:0 0 137px;
  width:137px;
  border-right:1px solid rgba(7,17,27,.1);
  text-align: center;
}
.ratings-leftscore .score{
  font-size: 24px;
  color:rgb(255,153,0);
  line-height: 28px;
  margin-bottom:6px;
}
.ratings-leftscore .title{
  font-size: 12px;
  color:rgb(7,17,27);
  line-height: 18px;
  margin-bottom:8px;
  font-weight: 900;
}
.ratings-leftscore .text{
  font-size: 10px;
  color:rgba(0,0,0,.7);
  line-height: 10px;
  margin-bottom: 6px;
}
.ratings-rightscore{
  flex:1;
  padding:0 18px;
}
.ratings-rightscore .boxcenter{
  margin:0 auto;
}
.ratings-rightscore .score{
  margin-bottom: 8px;
  text-align: center;  
}
.ratings-rightscore .score .title{
  display: inline-block;
  font-size: 12px;
  color:rgb(7,17,27);
  line-height: 18px;
  margin-right: 6px;
  vertical-align: bottom;
}
.ratings-rightscore .score .starbox{
  display: inline-block;
}
.ratings-rightscore .score .star{
  vertical-align: bottom;
  width:100%; 
}
.ratings-rightscore .score .score-time{
  display: inline-block;
  font-size: 12px;
  color:rgb(147,153,159);
  line-height: 18px;
}
@media only screen and (max-width: 370px){
  .ratings-leftscore{
    flex: 0 0 120px;
    width: 120px;
  }
  .ratings-rightscore{
    padding: 0px 2px;
  }
  .ratings-rightscore .score .title{
    margin-right:0px;
  }
  .starbox{
    width:140px;
  }
}
.ratingselect{
  background: white;
  margin-top:16px;
  padding:0 18px;
}
.rating-wrapper{
  padding: 0 18px;
  background: white;
}
.rating-wrapper .rating-item{
  display: flex;
  padding: 18px 0;
  border-bottom:1px solid rgba(7,17,27,.1);
}
.rating-wrapper .avatar{
  flex: 0 0 28px;
  width: 28px;
  margin-right: 12px;
}
.rating-wrapper .avatar img{
  border-radius: 50%;
}
.rating-wrapper .content{
  position: relative;
  flex:1;
}
.rating-wrapper .content .name{
  margin-bottom: 4px;
  line-height: 12px;
  font-size: 10px;
  color: rgb(7, 17, 27);
}
.rating-wrapper .content .star .el-rate__icon,.rating-wrapper .content .star .el-rate__text{
  font-size: 3px;
}
.rating-wrapper .content .text{
  margin-bottom: 8px;
  line-height: 18px;
  color: rgb(7, 17, 27);
  font-size: 12px;
}
.rating-wrapper .content .recommend{
  line-height: 16px;
  font-size: 0;
}
.rating-wrapper .content .recommend .iconfont,.rating-wrapper .content .recommend .item{
  display: inline-block;
  margin: 0 8px 4px 0;
  font-size: 9px;
}
.rating-wrapper .content .recommend .icon-zantongfill{
  color: rgb(0, 160, 220);
}
.rating-wrapper .content .recommend .icon-zan1{
  color:darkgray;
}
.rating-wrapper .content .recommend .item{
  padding: 0 6px;
  border: 1px solid rgba(7, 17, 27, 0.1);
  border-radius: 1px;
  color: rgb(147, 153, 159);
  background: #fff;
}
.rating-wrapper .content .mytime{
  position: absolute;
  top: 0;
  right: 0;
  line-height: 12px;
  font-size: 10px;
  color: rgb(147, 153, 159);
}
</style>
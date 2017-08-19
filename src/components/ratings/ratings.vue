<template>
  <div class="ratings" ref="ratings">
    <div class="rating-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect @select="ratingtype" @toggle="toggleContent" :selectType="selectType" :onlyContent="onlyContent" :ratings="ratings"></ratingselect>
      <div class="rating-wrapper">
        <ul>
          <li class="rating-item border-1px" v-for="rating in ratings" v-show="needShow(rating.rateType, rating.text)">
            <div class="avatar">
              <img :src="rating.avatar" width="28" height="28">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                <span class="icon-thumb_up"></span>
                <span class="item" v-for="item in rating.recommend">{{item}}</span>
              </div>
              <div class="time">{{rating.time | formatDate}}</div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
  import star from '../star/star'
  import split from '../split/split'
  import ratingselect from '../ratingselect/ratingselect'
  import {formatDate} from '../../common/js/data'
  import BScroll from 'better-scroll'

  const ALL = 2
  const ERR_OK = 0

  export default {
    name: 'ratings',
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: true
      }
    },
    created () {
      this.$http.get('/api/ratings').then(function (response) {
        response = response.body
        if (response.errno === ERR_OK) {
          this.ratings = response.ratings
          this.$nextTick(() => {
            this.scroll = new BScroll(this.$refs.ratings, {
              click: true
            })
          })
        }
      })
    },
    filters: {
      formatDate (time) {
        let date = new Date()
        return formatDate(date, 'yyyy-MM-dd hh:mm')
      }
    },
    methods: {
      needShow (type, text) {
        if (this.onlyContent && !text) {
          return false
        }
        if (this.selectType === ALL) {
          return true
        } else {
          return type === this.selectType
        }
      },
      ratingtype (type) {
        this.selectType = type
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      },
      toggleContent (onlyContent) {
        this.onlyContent = !this.onlyContent
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      }
    },
    components: {
      star,
      split,
      ratingselect
    }
  }
</script>

<style>
  .ratings {
    position: absolute;
    top: 174px;
    bottom: 0;
    width: 100%;
    overflow: hidden;
  }

  .ratings .overview {
    display: flex;
    padding: 18px 0;
  }

  .ratings .overview-left {
    flex: 0 0 137px;
    padding: 6px 0;
    width: 137px;
    border-right: 1px solid rgba(7, 17, 27, 0.1);
    text-align: center;
  }

  .ratings .overview-left .score {
    margin-bottom: 6px;
    line-height: 28px;
    font-size: 24px;
    color: rgb(255, 153, 0);
  }

  .ratings .overview-left .title {
    margin-bottom: 8px;
    line-height: 12px;
    font-size: 12px;
    color: rgb(7, 17, 27);
  }

  .ratings .overview-left .rank {
    line-height: 10px;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }

  .ratings .overview-right {
    flex: 1;
    padding: 6px 0 6px 24px;
  }

  .ratings .overview-right .score-wrapper {
    margin-bottom: 8px;
    font-size: 0;
  }

  .ratings .score-wrapper .title {
    display: inline-block;
    vertical-align: top;
    line-height: 18px;
    font-size: 12px;
    color: rgb(7, 17, 27);
  }

  .ratings .score-wrapper .star {
    display: inline-block;
    margin: 0 12px;
    vertical-align: top;
  }

  .ratings .score-wrapper .score {
    display: inline-block;
    vertical-align: top;
    line-height: 18px;
    font-size: 12px;
    color: rgb(255, 153, 0);
  }

  .ratings .delivery-wrapper {
    font-size: 0;
  }

  .ratings .delivery-wrapper .title {
    line-height: 18px;
    font-size: 12px;
    color: rgb(7, 17, 27);
  }

  .ratings .delivery-wrapper .delivery {
    margin-left: 12px;
    font-size: 12px;
    line-height: 18px;
    color: rgb(147, 153, 159);
  }

  @media only screen and (max-width: 320px) {
    .ratings .overview-left {
      flex: 0 0 115px;
      width: 115px;
    }

    .ratings .overview-right {
      padding-left: 12px;
    }

    .ratings .score-wrapper .star {
      margin: 0 6px;
    }
  }

  .rating-wrapper {
    padding: 0 18px;
  }

  .rating-wrapper .rating-item {
    display: flex;
    padding: 18px 0;
    position: relative;
  }

  .rating-wrapper .rating-item:after {
    display: block;
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    border-top: 1px solid rgba(7, 17, 27, 0.1);
    content: '';
  }

  @media (-webkit-min-device-pixel-ratio: 1.5), (min-device-pixel-ratio: 1.5) {
    .border-1px::after {
      -webkit-transform: scaleY(0.7);
      transform: scaleY(0.7);
    }
  }

  @media (-webkit-min-device-pixel-ratio: 2), (min-device-pixel-ratio: 2) {
    .border-1px::after {
      -webkit-transform: scaleY(0.5);
      transform: scaleY(0.5);
    }
  }

  .rating-wrapper .rating-item .avatar {
    flex: 0 0 28px;
    width: 28px;
    margin-right: 12px;
  }

  .rating-wrapper .avatar img {
    border-radius: 50%;
  }

  .rating-wrapper .rating-itme .content {
    position: relative;
    flex: 1;
  }

  .rating-wrapper .content .name {
    margin-bottom: 4px;
    line-height: 12px;
    font-size: 10px;
    color: rgb(7, 17, 27);
  }

  .rating-wrapper .content .star-wrapper {
    margin-bottom: 6px;
    font-size: 0;
  }

  .rating-wrapper .content .star {
    display: inline-block;
    vertical-align: top;
    margin-right: 6px;
  }

  .rating-wrapper .content .delivery {
    display: inline-block;
    vertical-align: top;
    font-size: 10px;
    line-height: 12px;
    color: rgb(147, 153, 159);
  }

  .rating-wrapper .content .text {
    margin-bottom: 8px;
    line-height: 18px;
    color: rgb(7, 17, 27);
    font-size: 12px;
  }

  .rating-wrapper .content .recommend {
    line-height: 16px;
    font-size: 0;
  }

  .rating-wrapper .content .recommend .icon-thumb_up,
  .rating-wrapper .content .recommend .item {
    display: inline-block;
    margin: 0 8px 4px 0;
    font-size: 9px;
  }

  .rating-wrapper .content .recommend .icon-thumb_up {
    color: rgb(0, 160, 220);
  }

  .rating-wrapper .content .recommend .item {
    padding: 0 6px;
    border: 1px solid rgba(7, 17, 27, 0.1);
    border-radius: 1px;
    color: rgb(147, 153, 159);
    background-color: #fff;
  }

  .rating-wrapper .content .time {
    position: absolute;
    top: 18px;
    right: 0;
    line-height: 12px;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }
</style>

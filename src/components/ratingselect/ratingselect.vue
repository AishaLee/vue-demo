<template>
  <div class="ratingselect">
    <div class="rating-type border-1px">
      <span @click="select(2, $event)" class="block positive" :class="{'active':selectType===2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
      <span @click="select(0, $event)" class="block positive" :class="{'active':selectType===0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
      <span @click="select(1, $event)" class="block negative" :class="{'active':selectType===1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
    </div>
    <div @click="toggleContent($event)" class="switch" :class="{'on': onlyContent}">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script>
  const POSITIVE = 0
  const NEGATIVE = 1
  const ALL = 2

  export default {
    name: 'ratingselect',
    props: {
      ratings: {
        type: Array,
        default () {
          return []
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
          }
        }
      }
    },
    computed: {
      positives () {
        return this.ratings.filter((rating) => {
          return rating.rateType === POSITIVE
        })
      },
      negatives () {
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE
        })
      }
    },
    methods: {
      select (type, event) {
        if (!event._constructed) {
          return
        }
        // this.selectType = type
        this.$emit('select', type)
      },
      toggleContent (event) {
        if (!event._constructed) {
          return
        }
        // this.onlyContent = !this.onlyContent
        this.$emit('toggle')
      }
    }
  }
</script>

<style>
  .ratingselect .rating-type {
    padding: 18px 0;
    margin: 0 18px;
    font-size: 0;
    position: relative;
  }

  .ratingselect .rating-type:after {
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

  .ratingselect .rating-type .block {
    display: inline-block;
    padding: 8px 12px;
    margin-right: 8px;
    border-radius: 1px;
    line-height: 16px;
    font-size: 12px;
    color: rgb(77, 85, 93);
  }

  .ratingselect .rating-type .block.active {
    color: #fff;
  }

  .ratingselect .rating-type .block .count {
    margin-left: 2px;
    font-size: 8px;
  }

  .ratingselect .rating-type .block.positive {
    background-color: rgba(0, 160, 220, 0.2);
  }

  .ratingselect .rating-type .block.positive.active {
    background-color: rgb(0, 160, 220);
  }

  .ratingselect .rating-type .block.negative {
    background-color: rgba(77, 85, 93, 0.2);
  }

  .ratingselect .rating-type .block.negative.active {
    background-color: rgb(77, 85, 93);
  }

  .ratingselect .switch {
    padding: 12px 18px;
    line-height: 24px;
    border-bottom: 1px solid rgba(7, 17, 27, 0.1);
    color: rgb(147, 153, 159);
    font-size: 0;
  }

  .ratingselect .switch .icon-check_circle {
    display: inline-block;
    vertical-align: top;
    font-size: 24px;
    margin-right: 4px;
  }

  .ratingselect .switch .text {
    display: inline-block;
    vertical-align: top;
    font-size: 12px;
  }

  .ratingselect .switch.on .icon-check_circle {
    color: #00c850;
  }
</style>

<template>
  <div>
    <div class="goods">
      <div class="menu-wrapper" ref="menuWrapper">
        <ul>
          <li v-for="(item, index) in goods" class="menu-item" :class="{'current':currentIndex===index}" @click="selectMenu(index, $event)">
            <span class="text border-1px">
              <i v-show="item.type > 0" class="icon" :class="classMap[item.type]"></i>{{item.name}}
            </span>
          </li>
        </ul>
      </div>
      <div class="foods-wrapper" ref="foodsWrapper">
        <ul>
          <li v-for="item in goods" class="foods-list foods-list-hook">
            <h1 class="title">{{item.name}}</h1>
            <ul>
              <li @click="selectFood(food, $event)" v-for="food in item.foods" class="foods-item border-1px">
                <div class="icon">
                  <img :src="food.icon" height="57">
                </div>
                <div class="content">
                  <h2 class="name">{{food.name}}</h2>
                  <p class="desc">{{food.description}}</p>
                  <div class="extra">
                    <span class="count">月售{{food.sellCount}}份</span>
                    <span>好评率{{food.rating}}%</span>
                  </div>
                  <div class="price">
                    <span class="now">￥{{food.price}}</span>
                    <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                  </div>
                  <div class="cartcontrol-wrapper">
                    <cartcontrol @cart-add="cartAdd" :food="food"></cartcontrol>
                  </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <shopcart ref="shopcart" :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    </div>
    <food @cart-add="cartAdd" :food="selectedFood" ref="food"></food>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'
  import shopcart from '../shopcart/shopcart'
  import cartcontrol from '../cartcontrol/cartcontrol'
  import food from '../food/food'

  var ERR_OK = 0

  export default {
    name: 'goods',
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
        selectedFood: {}
      }
    },
    computed: {
      currentIndex () {
        for (var i = 0; i < this.listHeight.length; i++) {
          var height1 = this.listHeight[i]
          var height2 = this.listHeight[i + 1]
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i
          }
        }
        return 0
      },
      selectFoods () {
        var foods = []
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food)
            }
          })
        })
        return foods
      }
    },
    created () {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']

      this.$http.get('/api/goods').then(function (response) {
        response = response.body
        if (response.errno === ERR_OK) {
          this.goods = response.goods

          this.$nextTick(function () {
            this._initScroll()
            this._calculateHeight()
          })
        }
      }, function (error) {
        console.log(error)
      })
    },
    methods: {
      selectMenu (index, event) {
        // 浏览器原生点击事件没有下面属性，better-scroll有这个属性
        if (!event._constructed) {
          return
        }
        var foodList = this.$refs.foodsWrapper.getElementsByClassName('foods-list-hook')
        var el = foodList[index]
        this.foodScroll.scrollToElement(el, 300)
      },
      selectFood (food, event) {
        if (!event._constructed) {
          return
        }
        this.selectedFood = food
        this.$refs.food.show()
      },
      _initScroll () {
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        })

        // 属性probeType：3 实时告诉我们滚动的位置
        this.foodScroll = new BScroll(this.$refs.foodsWrapper, {
          click: true,
          probeType: 3
        })

        this.foodScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      _calculateHeight () {
        var foodList = this.$refs.foodsWrapper.getElementsByClassName('foods-list-hook')
        var height = 0
        this.listHeight.push(height)
        for (var i = 0; i < foodList.length; i++) {
          var item = foodList[i]
          height += item.clientHeight
          this.listHeight.push(height)
        }
      },
      cartAdd (target) {
        this._drop(target)
      },
      _drop (target) {
        // 体验优化,异步执行下落动画
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target)
        })
      }
    },
    components: {
      shopcart,
      cartcontrol,
      food
    }
  }
</script>

<style>
  .goods {
    display: flex;
    position: absolute;
    top: 174px;
    bottom: 46px;
    width: 100%;
    overflow: hidden;
  }

  .goods .menu-wrapper {
    flex: 0 0 80px;
    width: 80px;
    background-color: #f3f5f7;
  }

  .goods .menu-item {
    display: table;
    height: 54px;
    width: 56px;
    line-height: 14px;
    padding: 0 12px;
  }

  .goods .menu-item.current {
    position: relative;
    z-index: 10;
    margin-top: -1px;
    background-color: #fff;
    font-weight: 700;
  }

  .goods .menu-item.current .text:after {
    display: none;
  }

  .goods .menu-item .icon {
    display: inline-block;
    width: 12px;
    height: 12px;
    margin-right: 4px;
    background-size: 12px 12px;
    background-repeat: no-repeat;
    vertical-align: middle;
  }

  .goods .menu-item .icon.decrease {
    background-image: url('./icon/decrease_1@2x.png');
  }

  .goods .menu-item .icon.discount {
    background-image: url('./icon/discount_1@2x.png');
  }

  .goods .menu-item .icon.guarantee {
    background-image: url('./icon/guarantee_1@2x.png');
  }

  .goods .menu-item .icon.invoice {
    background-image: url('./icon/invoice_1@2x.png');
  }

  .goods .menu-item .icon.special {
    background-image: url('./icon/special_1@2x.png');
  }

  .goods .menu-item .text {
    display: table-cell;
    width: 56px;
    font-size: 12px;
    vertical-align: middle;
    position: relative;
  }

  .goods .menu-item .text:after {
    display: block;
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    border-top: 1px solid rgba(7, 17, 27, 0.1);
    content: '';
  }

  .goods .foods-wrapper {
    flex: 1;
  }

  .goods .foods-wrapper .title {
    padding-left: 14px;
    height: 26px;
    line-height: 26px;
    border-left: 2px solid #d9dde1;
    font-size: 12px;
    color: rgb(147, 153, 159);
    background-color: #f3f5f7;
  }

  .goods .foods-item {
    display: flex;
    margin: 18px;
    padding-bottom: 18px;
    position: relative;
  }

  .goods .foods-item:after {
    display: block;
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    border-top: 1px solid rgba(7, 17, 27, 0.1);
    content: '';
  }

  .goods .foods-item:last-child:after {
    display: none;
  }

  .goods .foods-item:last-child {
    margin-bottom: 0;
  }

  .goods .foods-item .icon {
    flex: 0 0 57px;
    margin-right: 10px;
  }

  .goods .foods-item .content {
    flex: 1;
  }

  .goods .foods-item .content .name {
    margin: 2px 0 8px 0;
    height: 14px;
    line-height: 14px;
    font-size: 14px;
    color: rgb(7, 17, 27);
  }

  .goods .foods-item .content .desc,
  .goods .foods-item .content .extra {
    line-height: 16px;
    font-size: 10px;
    color: rgb(7, 17, 27);
  }

  .goods .foods-item .content .desc {
    margin-bottom: 8px;
  }

  .goods .foods-item .content .extra .count {
    margin-right: 12px;
  }

  .goods .foods-item .content .price {
    font-weight: 700;
    line-height: 24px;
  }

  .goods .foods-item .content .cartcontrol-wrapper {
    position: absolute;
    right: 0;
    bottom: 12px;
  }

  .goods .foods-item .content .price .now {
    margin-right: 8px;
    font-size: 14px;
    color: rgb(240, 20, 20);
  }

  .goods .foods-item .content .price .old {
    text-decoration: line-through;
    font-size: 10px;
    color: rgb(147, 153, 159);
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

  @media (-webkit-min-device-pixel-ratio: 3), (min-device-pixel-ratio: 3) {
    .goods .menu-item .icon.decrease {
      background-image: url('./icon/decrease_1@3x.png');
    }

    .goods .menu-item .icon.discount {
      background-image: url('./icon/discount_1@3x.png');
    }

    .goods .menu-item .icon.guarantee {
      background-image: url('./icon/guarantee_1@3x.png');
    }

    .goods .menu-item .icon.invoice {
      background-image: url('./icon/invoice_1@3x.png');
    }

    .goods .menu-item .icon.special {
      background-image: url('./icon/special_1@3x.png');
    }
  }
</style>

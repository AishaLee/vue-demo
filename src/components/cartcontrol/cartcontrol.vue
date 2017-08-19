<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" v-show="food.count > 0" @click.stop.prevent="decreaseCart($event)">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count > 0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addCart($event)"></div>
  </div>
</template>

<script>
  import Vue from 'vue'

  export default {
    name: 'cartcontrol',
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart (event) {
        if (!event._constructed) {
          return
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1)
        } else {
          this.food.count++
        }
        // 把当前点击的cart-add DOM对象传递给其他组件
        this.$emit('cart-add', event.target)
      },
      decreaseCart (event) {
        if (!event._constructed) {
          return
        }
        if (this.food.count) {
          this.food.count--
        }
      }
    }
  }
</script>

<style>
  .cartcontrol {
    font-size: 0;
  }

  .cartcontrol .cart-decrease {
    display: inline-block;
    padding: 6px;
    opacity: 1;
    transform: translate3d(0, 0, 0);
  }

  .cartcontrol .cart-decrease.move-enter-active,
  .cartcontrol .cart-decrease.move-leave-active {
    transition: all 0.4s linear;
  }

  .cartcontrol .cart-decrease .inner {
    display: inline-block;
    line-height: 24px;
    font-size: 24px;
    color: rgb(0, 160, 220);
    transition: all 0.4s linear;
    transform: rotate(0);
  }

  .cartcontrol .cart-decrease.move-enter,
  .cartcontrol .cart-decrease.move-leave-active {
    opacity: 0;
    transform: translate3d(24px, 0, 0);
  }

  .cartcontrol .cart-decrease.move-enter .inner,
  .cartcontrol .cart-decrease.move-leave-active .inner {
    transform: rotate(180deg);
  }

  .cartcontrol .cart-count {
    display: inline-block;
    vertical-align: top;
    width: 12px;
    padding-top: 12px;
    line-height: 12px;
    text-align: center;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }

  .cartcontrol .cart-add {
    display: inline-block;
    line-height: 24px;
    font-size: 24px;
    padding: 6px;
    color: rgb(0, 160, 220);
  }

  @media only screen and (max-width: 320px) {
    .cartcontrol .cart-decrease .inner,
    .cartcontrol .cart-add {
      line-height: 16px;
      font-size: 16px;
    }

    .cartcontrol .cart-count {
      padding-top: 8px;
    }
  }
</style>

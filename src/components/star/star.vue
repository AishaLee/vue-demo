<template>
  <div class="star" :class="starType">
    <span v-for="itemClass in itemClasses" :class="itemClass" class="star-item" track-by="$index"></span>
  </div>
</template>

<script>
  var LENGTH = 5
  var CLS_ON = 'on'
  var CLS_HALF = 'half'
  var CLS_OFF = 'off'

  export default {
    name: 'star',
    props: {
      size: {
        type: Number
      },
      score: {
        type: Number
      }
    },
    computed: {
      starType () {
        return 'star-' + this.size
      },
      itemClasses () {
        var result = []
        var score = Math.floor(this.score * 2) / 2
        var hasDecimal = score % 1 !== 0
        var integer = Math.floor(score)
        for (var i = 0; i < integer; i++) {
          result.push(CLS_ON)
        }
        if (hasDecimal) {
          result.push(CLS_HALF)
        }
        while (result.length < LENGTH) {
          result.push(CLS_OFF)
        }
        return result
      }
    }
  }
</script>

<style>
  .star {
    font-size: 0;
  }

  .star .star-item {
    display: inline-block;
    background-repeat: no-repeat;
  }

  .star.star-48 .star-item {
    width: 20px;
    height: 20px;
    margin-right: 22px;
    background-size: 20px 20px;
  }

  .star.star-48 .star-item.on {
    background-image: url('./icon/star48_on@2x.png');
  }

  .star.star-48 .star-item.off {
    background-image: url('./icon/star48_off@2x.png');
  }

  .star.star-48 .star-item.half {
    background-image: url('./icon/star48_half@2x.png');
  }

  .star.star-48 .starr-item:last-child {
    margin-right: 0;
  }

  .star.star-36 .star-item {
    width: 15px;
    height: 15px;
    margin-right: 6px;
    background-size: 15px 15px;
  }

  .star.star-36 .star-item.on {
    background-image: url('./icon/star36_on@2x.png');
  }

  .star.star-36 .star-item.off {
    background-image: url('./icon/star36_off@2x.png');
  }

  .star.star-36 .star-item.half {
    background-image: url('./icon/star36_half@2x.png');
  }

  .star.star-36 .starr-item:last-child {
    margin-right: 0;
  }

  .star.star-24 .star-item {
    width: 10px;
    height: 10px;
    margin-right: 3px;
    background-size: 10px 10px;
  }

  .star.star-24 .star-item.on {
    background-image: url('./icon/star24_on@2x.png');
  }

  .star.star-24 .star-item.off {
    background-image: url('./icon/star24_off@2x.png');
  }

  .star.star-24 .star-item.half {
    background-image: url('./icon/star24_half@2x.png');
  }

  .star.star-24 .starr-item:last-child {
    margin-right: 0;
  }

  @media (-webkit-min-device-pixel-ratio: 3), (min-device-pixel-ratio: 3) {
    .star.star-48 .star-item.on {
      background-image: url('./icon/star48_on@3x.png');
    }

    .star.star-48 .star-item.off {
      background-image: url('./icon/star48_off@3x.png');
    }

    .star.star-48 .star-item.half {
      background-image: url('./icon/star48_half@3x.png');
    }

    .star.star-36 .star-item.on {
      background-image: url('./icon/star36_on@3x.png');
    }

    .star.star-36 .star-item.off {
      background-image: url('./icon/star36_off@3x.png');
    }

    .star.star-36 .star-item.half {
      background-image: url('./icon/star36_half@3x.png');
    }

    .star.star-24 .star-item.on {
      background-image: url('./icon/star24_on@3x.png');
    }

    .star.star-24 .star-item.off {
      background-image: url('./icon/star24_off@3x.png');
    }

    .star.star-24 .star-item.half {
      background-image: url('./icon/star24_half@3x.png');
    }
  }
</style>

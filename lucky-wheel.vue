<template>
  <view v-if="isShow" class="lucky-box" :style="{ width: boxWidth + 'px', height: boxHeight + 'px' }">
    <canvas :style="{ width: boxWidth + 'px', height: boxHeight + 'px' }" canvas-id="lucky-wheel" id="lucky-wheel"></canvas>
    <view class="lucky-wheel-btn" @click="toPlay" :style="{ width: btnWidth + 'px', height: btnHeight + 'px' }"></view>
  </view>
</template>

<script>
  import { LuckyWheel } from 'lucky-canvas'
  export default {
    name: 'lucky-wheel',
    data () {
      return {
        isShow: false,
        boxWidth: 100,
        boxHeight: 100,
        btnWidth: 0,
        btnHeight: 0,
      }
    },
    props: {
      width: {
        type: String,
        default: '600rpx'
      },
      height: {
        type: String,
        default: '600rpx'
      },
      blocks: {
        type: Array,
        default: () => []
      },
      prizes: {
        type: Array,
        default: () => []
      },
      buttons: {
        type: Array,
        default: () => []
      },
      defaultConfig: {
        type: Object,
        default: () => {
          return {}
        }
      },
      defaultStyle: {
        type: Object,
        default: () => {
          return {}
        }
      },
    },
    mounted () {
      this.init()
    },
    watch: {
      prizes: {
        handler (newData, oldData) {
          this.$lucky.prizes = newData
        },
        deep: true
      },
      buttons: {
        handler (newData, oldData) {
          this.$lucky.buttons = newData
        },
        deep: true
      }
    },
    methods: {
      init () {
        this.boxWidth = this.changeUnits(this.width)
        this.boxHeight = this.changeUnits(this.height)
        this.isShow = true
        this.$nextTick(() => {
          this.draw()
        })
      },
      draw () {
        const ctx = this.ctx = uni.createCanvasContext('lucky-wheel', this)
        const dpr = this.dpr = uni.getSystemInfoSync().pixelRatio
        const $lucky = this.$lucky = new LuckyWheel({
          // #ifdef H5
          flag: 'UNI-H5',
          // #endif
          // #ifdef MP
          flag: 'UNI-MINI-WX',
          // #endif
          ctx: ctx,
          width: this.width,
          height: this.height,
          setTimeout: setTimeout,
          clearTimeout: clearTimeout,
          setInterval: setInterval,
          clearInterval: clearInterval,
        }, {
          ...this.$props,
          start: (...rest) => {
            this.$emit('start', ...rest)
          },
          end: (...rest) => {
            this.$emit('end', ...rest)
          },
        })
        // 动态设置按钮大小
        this.btnWidth = $lucky.maxBtnRadius * 2
        this.btnHeight = $lucky.maxBtnRadius * 2
      },
      toPlay (e) {
        this.$lucky.startCallback()
      },
      play (...rest) {
        this.$lucky.play(...rest)
      },
      stop (...rest) {
        this.$lucky.stop(...rest)
      },
      rpx2px (value) {
        if (typeof value === 'string') value = Number(value.replace(/[a-z]*/g, ''))
        return uni.getSystemInfoSync().windowWidth / 750 * value
      },
      changeUnits (value) {
        return Number(value.replace(/^(\-*[0-9.]*)([a-z%]*)$/, (value, num, unit) => {
          switch (unit) {
            case 'px':
              num *= 1
              break
            case 'rpx':
              num = this.rpx2px(num)
              break
            default:
              num *= 1
              break
          }
          return num
        }))
      }
    },
  }
</script>

<style scoped>
  .lucky-box {
    position: relative;
  }
  canvas {
    position: absolute;
  }
  .lucky-wheel-btn {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    background: rgba(0, 0, 0, 0);
    border-radius: 50%;
  }
</style>

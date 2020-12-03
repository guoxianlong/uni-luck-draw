<template>
	<view v-if="isShow" class="lucky-box" :style="{ width: boxWidth + 'px', height: boxHeight + 'px' }">
    <canvas
      id="lucky-grid"
      canvas-id="lucky-grid"
      :style="{ width: boxWidth + 'px', height: boxHeight + 'px' }"
    ></canvas>
    <view class="lucky-grid-btn" @click="toPlay" :style="{
      left: btnLeft + 'px',
      top: btnTop + 'px',
      width: btnWidth + 'px',
      height: btnHeight + 'px',
    }"></view>
	</view>
</template>

<script>
  import { LuckyGrid } from 'lucky-canvas'
  export default {
    name: 'lucky-grid',
    data () {
      return {
        isShow: false,
        boxWidth: 100,
        boxHeight: 100,
        btnWidth: 0,
        btnHeight: 0,
        btnLeft: 0,
        btnTop: 0,
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
      cols: {
        type: [String, Number],
        default: 3,
      },
      rows: {
        type: [String, Number],
        default: 3,
      },
      blocks: {
        type: Array,
        default: () => []
      },
      prizes: {
        type: Array,
        default: () => []
      },
      button: {
        type: Object,
        default: () => {
          return {}
        }
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
      activeStyle: {
        type: Object,
        default: () => {
          return {}
        }
      }
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
      button: {
        handler (newData, oldData) {
          this.$lucky.button = newData
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
        const ctx = this.ctx = uni.createCanvasContext('lucky-grid', this)
        const dpr = this.dpr = uni.getSystemInfoSync().pixelRatio
        console.log(dpr)
        const $lucky = this.$lucky = new LuckyGrid({
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
        const button = this.$props.button
        if (button) {
          const [x, y, width, height] = $lucky.getGeometricProperty([
            button.x,
            button.y,
            button.col || 1,
            button.row || 1
          ])
          console.log([x, y, width, height])
          this.btnLeft = x
          this.btnTop = y
          this.btnWidth = width
          this.btnHeight = height
        }
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
    overflow: hidden;
  }
  canvas {
    position: absolute;
    left: 0;
    top: 0;
  }
  .lucky-grid-btn {
    position: absolute;
    background: rgba(0, 0, 0, 0);
    border-radius: 0;
  }
</style>

<template>
  <div :style="{width: systemInfo.width+'px', height: systemInfo.height+'px', backgroundColor: bgcolor}"
        style="position: absolute; left:0; top:0; z-index:9998; overflow: hidden;">

    <div v-for="item in skeletonRectLists"
          :class="{chiaroscuro: loading == 'chiaroscuro'}"
          :style="{width: item.width+'px', height: item.height+'px', left: item.left+'px', top: item.top+'px'}"
          style="background-color: rgb(194, 207, 214); position: absolute;"
          :key="index"></div>

    <div v-for="item in skeletonCircleLists"
          :class="{chiaroscuro: loading == 'chiaroscuro'}"
          :style="{width: item.width+'px', height: item.height+'px', left: item.left+'px', top: item.top+'px', borderRadius: item.width+'px'}"
          style="background-color: rgb(194, 207, 214); position: absolute;"
          :key="index"></div>

    <div class="spinbox" v-if="loading == 'spin'">
      <div class="spin"></div>
    </div>

  </div>
</template>

<script>
  /**
   *  <div class="page skeleton">
   *    <skeleton v-if="showSkeleton" :selector="'skeleton'" :loading="'spin'" :bgcolor="'#FFF'"/>
   *    矩形区域用类名 skeleton-rect
   *    圆形区域用类名 skeleton-radius
   *  </div>
   */
  export default {
    name: 'skeleton',
    props: {
      bgcolor: { type: String, default: '#FFF' },
      selector: { type: String, default: 'skeleton' },
      loading: { type: String, default: 'spin' }
    },
    data() {
      return {
        loadingAni: ['spin', 'chiaroscuro'],
        systemInfo: {},
        skeletonRectLists: [],
        skeletonCircleLists: []
      }
    },
    methods: {
      rectHandle() {
        // 绘制不带样式的节点
        wx.createSelectorQuery().selectAll(`.${this.selector}-rect`).boundingClientRect().exec(res => {
          this.skeletonRectLists = res[0]
        })

      },
      radiusHandle() {
        wx.createSelectorQuery().selectAll(`.${this.selector}-radius`).boundingClientRect().exec(res => {
          this.skeletonCircleLists = res[0]
        })
      }
    },
    mounted() {
      // 默认的首屏宽高，防止内容闪现
      const { windowWidth, windowHeight } = wx.getSystemInfoSync()
      this.systemInfo = { width: windowWidth, height: windowHeight }
      this.loading = this.loadingAni.includes(this.loading) ? this.loading : 'spin'

      wx.createSelectorQuery().selectAll(`.${this.selector}`).boundingClientRect().exec(res => {
        this.systemInfo.height = res[0][0].height + res[0][0].top
      })
      // 绘制矩形
      this.rectHandle()
      // 绘制圆形
      this.radiusHandle()
    }
  }
</script>

<style lang="scss" scoped>
  .spinbox {
    position: fixed;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100%;
    width: 100%;
    z-index: 9999;
    .spin {
      display: inline-block;
      width: 64rpx;
      height: 64rpx;
      &:after {
        content: '';
        display: block;
        width: 46rpx;
        height: 46rpx;
        margin: 1rpx;
        border-radius: 50%;
        border: 5rpx solid #409eff;
        border-color: #409eff transparent #409eff transparent;
        animation: spin 1.2s linear infinite;
      }
    }
  }

  @keyframes spin {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }

  .chiaroscuro {
    width: 100%;
    height: 100%;
    background: rgb(194, 207, 214);
    animation-duration: 2s;
    animation-name: blink;
    animation-iteration-count: infinite;
  }

  @keyframes blink {
    0% {
      opacity: .4;
    }
    50% {
      opacity: 1;
    }
    100% {
      opacity: .4;
    }
  }

  @keyframes flush {
    0% {
      left: -100%;
    }
    50% {
      left: 0;
    }
    100% {
      left: 100%;
    }
  }

  .shine {
    animation: flush 2s linear infinite;
    position: absolute;
    top: 0;
    bottom: 0;
    width: 100%;
    background: linear-gradient(to left,
    rgba(255, 255, 255, 0) 0%,
    rgba(255, 255, 255, .85) 50%,
    rgba(255, 255, 255, 0) 100%
    )
  }
</style>

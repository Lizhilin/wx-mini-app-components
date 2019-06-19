<template>
  <swiper :indicator-dots="list.length > 1 ? assignConfig.indicatorDots : false"
          :autoplay="assignConfig.autoplay"
          :interval="assignConfig.interval"
          :duration="assignConfig.duration"
          :indicator-color="assignConfig.indicatorColor"
          :indicator-active-color="assignConfig.indicatorActiveColor"
          :circular="assignConfig.circular"
          :previous-margin="assignConfig.previousMargin"
          :next-margin="assignConfig.nextMargin"
          :style="{ height }"
          class="swiper"
  >
    <swiper-item v-for="(item, index) in list" :key="index" class="item">
      <img :src="item.pic || defImg"
           class="slide-image img"
           mode="aspectFill"
           @click="tapItem(item)"
      >
    </swiper-item>
  </swiper>
</template>

<script>
const DefaultConfig = {
  indicatorDots: true,
  autoplay: true,
  interval: 5000,
  duration: 300,
  circular: true,
  previousMargin: '0px',
  nextMargin: '0px',
  indicatorColor: 'rgba(245, 245, 245, 1)', // 指示点颜色
  indicatorActiveColor: 'rgba(42, 46, 62, 1)'
}

export default {
  name: 'MySwiper',
  props: {
    config: { default: () => ({}), type: Object },
    list: { default: () => [], type: Array },
    height: { default: '137px', type: String }
  },
  computed: {
    assignConfig() { return { ...DefaultConfig, ...this.config } }
  },
  methods: {
    tapItem(item) { this.$emit('tapSwiper', item) }
  }
}
</script>

<style lang="scss" scoped>
  @import "~@/assets/style/mixin.scss";

  .swiper {
    width: 100%;
    width: 100vw;
    position: relative;

    .item {
      height: 100%;

      .img {
        width: 100%;
        height: 100%;
        display: block;
      }
    }
  }

  .swiper {
    .wx-swiper-dots.wx-swiper-dots-horizontal {
      position: absolute;
      bottom: 5px;
    }

    .wx-swiper-dot {
      width: 6px;
      height: 6px;
      margin-left: 5px;
      display: inline-flex;
      justify-content: space-between;
      border-radius: 50%;
      /*background: #f5f5f5;*/

      &:before {
        /*display: none;*/
      }
    }

    /*.wx-swiper-dot-active {*/
      /*background: #2a2e3e;*/
    /*}*/

    /*.wx-swiper-dot-active:before {*/
      /*background: white;*/
    /*}*/
  }
</style>

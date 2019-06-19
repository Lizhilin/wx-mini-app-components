<template>
  <div class="u-viewNone">
    <div class="u-viewNone__icon">
      <img v-if="config.viewType"
           :src="imgMap[config.viewType]"
           :alt="config.viewText"
           :style="{
             width: config.imgSize.width || imgSize.width ,
             height: config.imgSize.height || imgSize.height
           }"
           class="pic"
      >
    </div>
    <div class="u-viewNone__text f28">
      <p>{{ config.viewText }}</p>
      <p>{{ config.viewTextSecond }}</p>
    </div>
    <div v-if="config.viewActionBtnText" class="u-viewNone__action f30" @click="viewAction">
      {{ config.viewActionBtnText }}
    </div>
  </div>
</template>

<script>
/**
 * @viewnone: Object
 * @viewaction: Function
 * @example:
 * <ViewNone v-if="viewNoneShow"
             :viewnone="ViewNoneConfig"
             :viewaction="tapViewNone" />
 *  components: { ViewNone }
 *  mixins: [ErrorMixin]
 *  this.errHandler(status, title, type)
*/
export default {
  name: 'ViewNone',
  props: {
    viewnone: { default: () => ({}), type: Object },
    viewaction: { default: null, type: Function }
  },
  data() {
    return {
      imgMap: {
        noOrderData: '/static/img/order_def.png',
        noBalanceData: '/static/img/yuer_def.png',
        noIntegralData: '/static/img/jifen_def.png',
        noListData: '/static/img/no_list_data.png',
        serverError: '/static/img/server_error.png',
        netError: '/static/img/network_error.png'
      },
      imgSize: { // 图片对应的大小
        width: '200rpx', height: '150rpx'
      },
      defaultConfig: {
        imgSize: {},
        viewName: '',
        viewText: '',
        viewTextSecond: '',
        viewActionBtnText: '',
        viewType: '' // 值在 imgMap 里取
      }
    }
  },
  computed: {
    config() {
      return {
        ...this.defaultConfig,
        ...this.viewnone
      }
    }
  },
  methods: {
    viewAction() {
      this.viewaction && this.viewaction()
    }
  }
}
</script>

<style scoped>
  .u-viewNone {
    text-align: center;
    min-height: 100vh;
  }

  .u-viewNone__icon .pic {
    margin: 55px 0 10px 0;
    width: 100px;
    height: 75px;
    background: none;
  }

  .u-viewNone__text {
    margin-bottom: 50px;
    font-size: 14px;
    color: #9b9b9b;
  }

  .u-viewNone__action {
    margin: 0 auto;
    width: 105px;
    height: 40px;
    font-size: 15px;
    line-height: 40px;
    text-align: center;
    border-radius: 40px;
    color: #ff4a57;
    border: 1px solid #ff4a57;
  }
</style>

<template>
  <div class="TouchMoveItem"
       @touchmove="touchmove"
       @touchstart="touchstart"
       @touchend="touchend"
       :style="{transform: 'translateX('+ itemX +'px)'}"
  >
    <slot/>
  </div>
</template>

<script>
let startX = null
let startY = null

export default {
  name: 'TouchMoveItem',
  props: {
    delBtnWidth: { default: 80, type: Number },
    // 所在列表唯一标识符
    ulTag: { default: 'ul', type: String },
    // 所在列表索引
    index: { default: 0, type: Number }
  },
  data() {
    return {
      itemX: 0
    }
  },
  computed: {
    minMove() { return this.delBtnWidth / 10 }
  },
  mounted() {
    // 外部重置未交互的item位置
    this.$EventBus.$on('resetItemX', (curTouchIndex, ulTag = 'ul') => {
      if (ulTag === this.ulTag && curTouchIndex !== this.index) {
        this.itemX = 0
      }
    })
  },
  methods: {
    touchstart(e) {
      this.$EventBus.$emit('resetItemX', this.index, this.ulTag)
      startX = e.touches[0].clientX
      startY = e.touches[0].clientY
    },
    touchmove(e) {
      let _disX = startX - e.touches[0].clientX
      let _disY = startY - e.touches[0].clientY
      if (_disX < _disY) { // 右滑
        this.itemX = 0
      } else { // 左滑
        if (_disX <= this.minMove) {
          this.itemX = 0
        } else {
          this.itemX = -_disX
          if (_disX >= this.delBtnWidth || _disX > this.delBtnWidth / 2) {
            this.itemX = -this.delBtnWidth
          }
        }
      }
    },
    touchend(e) {
      let _disX = startX - e.changedTouches[0].clientX
      let _disY = startY - e.changedTouches[0].clientY
      if (_disX < _disY) {
        this.itemX = 0
      } else {
        this.itemX = _disX > this.delBtnWidth / 2
          ? -this.delBtnWidth
          : 0
      }
    }
  }
}
</script>

<style lang="scss">
  .TouchMoveItem {
    transition: all .1s;
  }
</style>

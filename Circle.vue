<template>
  <div class="Circle" @touchstart="touchstart">
    <div :style="style" class="Circle-i" />
    <slot />
  </div>
</template>

<script>
export default {
  name: 'Circle',
  data() {
    return {
      style: ''
    }
  },
  methods: {
    touchstart(e) {
      const { clientX, clientY } = e.touches[0]
      const { offsetLeft, offsetTop } = e.currentTarget
      this.style = `animation: circle 1s; left: ${clientX - offsetLeft}px; top: ${clientY - offsetTop}px;`
      setTimeout(() => {
        this.style = ''
      }, 1000)
    }
  }
}
</script>

<style lang="scss">
  .Circle {
    position: relative;
    overflow: hidden;
  }
  .Circle-i {
    border-radius: 50%;
    position: absolute;
    z-index: 1;
    will-change: transform;
    background-image: radial-gradient(transparent, rgba(black, .1));
  }
  @keyframes circle {
    from {
      width: 2px;
      height: 2px;
    }
    to {
      transform: scale(700);
    }
  }
</style>

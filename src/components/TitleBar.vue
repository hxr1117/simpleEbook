<template>
  <transition name="slide-down">
    <div class="title-wrapper" v-show="ifTitleAndMenuShow">
      <div class="left">
        <span class="icon-back icon"></span>
      </div>
      <div class="right">
        <div class="icon-wrapper">
          <div class="icon-cart icon"></div>
        </div>
        <div class="icon-wrapper">
          <div class="icon-person icon"></div>
        </div>
        <div class="icon-wrapper">
          <div class="icon-more icon"></div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  props: {
    ifTitleAndMenuShow: {
      type: Boolean,
      default: false
    }
  }
}
</script>
<style lang="scss" scoped>
@import '../assets/styles/global';
.title-wrapper {
  // line-height: px2rem(48);
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 101;
  height: px2rem(48);
  background-color: white;
  display: flex;
  box-shadow: 0 px2rem(8) px2rem(8) rgba($color: #000000, $alpha: 0.15);
  .left {
    flex: 0 0 px2rem(60);
    @include center;
  }
  .right {
    flex: 1;
    display: flex;
    justify-content: flex-end;
    .icon-wrapper {
      flex: 0 0 px2rem(40);
      @include center;
    }
  }
  .icon {
    color: #333;
    font-size: px2rem(20);
  }
}
</style>

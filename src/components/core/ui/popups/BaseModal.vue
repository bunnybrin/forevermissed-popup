<template>
  <div v-if="isOpen" class="v-popup" @click="close">
    <div class="v-popup__content" @click.stop>
      <div class="v-popup__header">
        <h3 class="v-popup__title" v-if="title">{{ title }}</h3>
        <IconClose @click="close"/>
      </div>
      <slot name="content"/>
      <div class="v-popup__footer">
        <slot name="footer" :close="close" :confirm="confirm"/>
      </div>
    </div>
  </div>
</template>

<script>
import IconClose from '@/components/icons/IconClose.vue';

export default {
  name: 'BaseModal',
  components: { IconClose },

  props: {
    title: {
      type: String,
      default: '',
    },
  },
  currentPopupController: null,

  data () {
    return { isOpen: false };
  },

  methods: {
    open () {
      let resolve;
      let reject;

      const popupPromise = new Promise((ok, fail) => {
        resolve = ok;
        reject = fail;
      });

      this.$options.popupController = { resolve, reject };
      this.isOpen = true;

      return popupPromise;
    },

    confirm (payload = true) {
      this.$options.popupController.resolve(payload);
      this.isOpen = false;
    },

    close (payload = false) {
      this.$options.popupController.resolve(payload);
      this.isOpen = false;
    },
  },
};
</script>

<style lang="scss">
$offsetLeft: 24px;
$offsetRight: 23px;
.v-popup {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  background-color: var(--c-popup-background);
  z-index: 1000;

  &__title {
    margin-top: 23px;
    margin-bottom: 23px;
    font-weight: 600;
    font-size: 24px;
    font-feature-settings: 'ss01' on;
  }

  &__content {
    padding: 0 $offsetRight 23px $offsetLeft;
    margin: 45px auto 0 auto;
    max-width: 598px;
    width: 100%;
    background-color: var(--bgc-popup);
    border-radius: 6px;
  }

  &__header {
    display: flex;
    align-items: center;
    justify-content: space-between;

    .ico-close {
      margin-left: auto;
      width: 14px;
      margin-right: 6px;
      cursor: pointer;
    }
  }

  &__footer {
    position: relative;
    padding-top: 24px;

    &:before {
      content: '';
      position: absolute;
      top: 0;
      right: -$offsetRight;
      left: -$offsetLeft;
      height: 1px;
      background-color: var(--bgc-hr);
    }
  }
}
</style>

<template>
  <div class="v-dropdown" :class="{'is-open': isOpen}">
    <span
        class="v-dropdown__label"
        @click="toggleDropdown"
    >
      {{ value }}
    </span>

      <div class="v-dropdown__content">
        <slot
            name="content"
            v-for="(item, index) in items"
            :item="item"
            :index="index"
            :toggleDropdown="toggleDropdown"
        />
      </div>
  </div>
</template>

<script>
export default {
  name: 'BaseDropdown',

  props: {
    value: {
      type: String,
      required: true,
    },

    items: {
      type: Array,
      required: true,
    },
  },

  data () {
    return {
      isOpen: false,
    };
  },

  methods: {
    toggleDropdown () {
      if (this.isOpen) {
        this.isOpen = false;
      } else {
        this.isOpen = true;

        document.addEventListener('click', this.close, true);
      }
    },

    close (e) {
      document.removeEventListener('click', this.close, true);

      const elem = e.target.closest('.v-dropdown');

      this.isOpen = elem && elem.classList.contains('is-open');
    },
  },
};
</script>

<style lang="scss">
.v-dropdown {
  position: relative;
  cursor: pointer;

  &__content {
    display: none;
    position: absolute;
    right: 0;
    width: 318px;
    padding: 16px;
    border-radius: 6px;
  }

  &__label {
    text-transform: capitalize;
  }

  &.is-open {
    z-index: 1;

    .v-dropdown__content {
      display: block;
    }
  }
}
</style>

<template>
  <span
      @click="openDropdown"
      :class="['v-dropdown', {'is-open': isOpen}]"
  >
    {{ title }}
    <IconDropdown/>
  </span>
</template>

<script>
import IconDropdown from '@/components/icons/IconDropdown.vue';

export default {
  name: 'DropdownHandler',
  components: { IconDropdown },
  props: {
    title: {
      type: String,
      required: true,
    },
  },

  emits: {
    click: null
  },

  data () {
    return {
      isOpen: false,
    };
  },

  methods: {
    openDropdown (e) {
      this.isOpen = true;
      this.$emit('click', e)
    },

    close () {
      this.isOpen = false
    }
  },
};
</script>

<style lang="scss">
.v-dropdown {
  display: flex;
  align-items: center;
  text-transform: capitalize;
  padding-right: 5px;
  padding-left: 5px;
  color: var(--c-text);
  transition: color 300ms;
  cursor: pointer;

  .ico-dropdown {
    width: 9px;
    margin-left: 1px;
    pointer-events: none;
    fill: currentColor;
  }

  &.is-open {
    color: var(--c-text-active);

    .ico-dropdown {
      transform: scale(1, -1);
    }
  }
}
</style>

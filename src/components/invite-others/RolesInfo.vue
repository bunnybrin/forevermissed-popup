<template>
  <ul class="roles">
    <li
        class="roles__item"
        v-for="role in roles"
        @click="$emit('selectRole', role)"
    >
      <h4 class="roles__title">
        {{ role.title }}
        <IconCheck v-if="activeRole === role.id"/>
      </h4>
      <p class="roles__text"> {{ role.text }}</p>
    </li>
  </ul>
</template>

<script>
import IconCheck from '@/components/icons/IconCheck.vue';

export default {
  name: 'RolesInfo',
  components: { IconCheck },
  props: {
    activeRole: {
      type: String,
      default: '',
    },
    roles: {
      type: Array,
      default: [],
    },
  },

  emits: {
    selectRole: null,
  },
};
</script>

<style lang="scss">
.roles {
  $offsetBottom: 23px;
  position: absolute;
  margin-top: 23px;
  right: 72px;
  background-color: var(--c-white);
  padding-left: 0;
  max-width: 318px;
  width: 100%;
  box-shadow: var(--box-shadow);
  transition: background-color 300ms;
  border-radius: 6px;
  list-style: none;
  z-index: 1;

  &__item {
    padding: 16px 16px 16px 48px;

    &:hover {
      background-color: var(--bgc-invite-item);
      cursor: pointer;
    }

    &:last-child {
      .roles__title {
        margin-top: -10px;
      }
    }
  }

  &__title {
    margin-top: 0;
    margin-bottom: 3px;
    color: var(--role-title);
    font-weight: 600;
    font-size: 16px;
    line-height: 24px;
    position: relative;

    .ico-check {
      position: absolute;
      left: -32px;
      top: 8px;
      width: 15px;
      fill: var(--role-icon);
    }
  }

  &__text {
    margin-top: 0;
    margin-bottom: 0;
    font-weight: 400;
    font-size: 12px;
    line-height: 16px;
    color: var(--role-text);
  }
}

@media screen and (max-width: 430px) {
  .roles {
    max-width: 274px;
    right: 25px;
  }
}
</style>

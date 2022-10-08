<template>
  <div class="input-field">
    <input
        class="input-field__input"
        type="text"
        :placeholder="$attrs.placeholder"
        :value="value"
        @input="updateValue"
        @keydown.enter="$emit('save')"
        @focus="clearErrors"
    />
    <div
        class="input-field__errors"
        v-for="error in errors"
    >
      {{ error.message }}
    </div>
  </div>

</template>

<script>

import validationRules from '@/utils/validation/validation.js';

export default {
  name: 'InputField',

  props: {
    validations: {
      type: Array,
      default: [],
    },

    errors: {
      type: Array,
      default: [],
    },
    value: {
      type: String,
      default: '',
    },
  },

  validationRules: validationRules,

  errorMessages: {
    'email': 'Please enter a valid email address. For example johndoe@domain.com.',
  },

  emits: {
    'update:value': null,
    'update:errors': val => Array.isArray(val),
    'save': null,
  },

  inheritAttrs: false,

  methods: {
    updateValue (event) {
      this.$emit('update:value', event.target.value);
    },

    async validate () {
      return this.$nextTick(() => {
        return this.validations.every(validationKey => {
          const validationFunction = this.$options.validationRules[validationKey];
          const isValid = validationFunction(this.value);

          isValid ? this.clearErrors() : this.pushError(this.$options.errorMessages[validationKey])

          return isValid;
        });
      });
    },

    clearErrors () {
      this.$emit('update:errors', []);
    },

    pushError (message) {
      this.$emit('update:errors', [{ message}]);
    }

  },
};
</script>

<style lang="scss">
.input-field {
  &__input {
    width: 100%;
    padding: 15px 16px;
    font-size: 16px;
    border: 1px solid var(--border-color-input);
    border-radius: 6px;

    &::placeholder {
      color: var(--placeholder-color);
    }
  }

  &__errors {
    color: var(--c-erorrs);
  }
}
</style>

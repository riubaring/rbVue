<template>
  <div class="rbNumber">
    <div class="rbNumber__box">
      <input
        v-model="selectedValue"
        @keyup.stop="setNumber"
        @keydown.down.prevent="decreaseNumber()"
        @keydown.up.prevent="increaseNumber()"
        type="text"
        :disabled="disabled"
      >
    </div>
    <div
      class="rbNumber__button"
      :class="{'disabled' : disabled}"
      role="minus"
      @click="decreaseNumber()"
    >
      <i class="fas fa-minus" aria-hidden="true"></i>
    </div>
    <div
      class="rbNumber__button"
      :class="{'disabled' : disabled}"
      role="plus"
      @click="increaseNumber()"
    >
      <i class="fas fa-plus" aria-hidden="true"></i>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      selectedValue: this.value
    };
  },
  watch: {
    value(value) {
      this.setValue(value);
    }
  },
  methods: {
    decreaseNumber() {
      if (this.disabled) return;

      this.selectedValue--;
      if (this.selectedValue <= this.minValue) {
        this.selectedValue = this.minValue;
      }

      this.$emit("input", this.selectedValue);
    },
    increaseNumber() {
      if (this.disabled) return;

      this.selectedValue++;
      if (this.maxValue != null) {
        if (this.selectedValue >= this.maxValue) {
          this.selectedValue = this.maxValue;
        }
      }

      this.$emit("input", this.selectedValue);
    },
    setNumber() {
      if (this.disabled) return;

      this.$emit("input", parseFloat(this.selectedValue));
    },
    setValue(value) {
      this.selectedValue = value;
    }
  },
  props: {
    disabled: {
      type: Boolean,
      default: false
    },
    maxValue: {
      type: Number,
      default: null
    },
    minValue: {
      type: Number,
      default: 1
    },
    value: {
      type: Number,
      default: 1
    }
  }
};
</script>
<style lang="scss">
.rbNumber {
  min-width: 128px;
  padding-left: 26px;
  padding-right: 26px;
  position: relative;

  &__box {
    input {
      border: 2px solid #e2e8f0;
      padding: 0.5rem;
      text-align: center;
      width: 100%;
    }

    input[disabled] {
      color: #e2e8f0;
    }
  }
  &__button {
    color: #ffffff;
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    height: 30px;
    width: 24px;

    &.disabled {
      background-color: #f7fafc !important;
      color: #e2e8f0;
      cursor: default;

      &:hover {
        background-color: transparent;
      }
    }

    i.fas {
      font-size: 11.5px;
      position: absolute;
      right: 7px;
      top: 50%;
      transform: translateY(-50%);
      transition: opacity 0.3s;
    }

    &[role="plus"] {
      background-color: #63b3ed;
      right: 0;

      &:hover {
        background-color: #3182ce;
      }
    }

    &[role="minus"] {
      background-color: #f6ad55;
      left: 0;

      &:hover {
        background-color: #dd6b20;
      }
    }
  }
}
</style>
<template>
  <div
    class="dropdown"
    @focus="activate()"
    @blur="deactivate()"
    @keyup.esc="deactivate()"
    @keydown.self.down.prevent="pointerForward()"
    @keydown.self.up.prevent="pointerBackward()"
    @keypress.enter.tab.stop.self="addPointerElement($event)"
  >
    <div class="dropdown__header" :class="{'is-active' : isActive}">
      <input
        ref="dropdownInput"
        v-model="selectedLabel"
        class="dropdown__box"
        :class="{'text-left': textAlign === 'left', 'text-right': textAlign === 'right'}"
        type="text"
        @input="updateKeyword($event.target.value)"
        @focus.prevent="activate()"
        @blur.prevent="deactivate()"
        @keyup.esc="deactivate()"
        @keydown.down.prevent="pointerForward()"
        @keydown.up.prevent="pointerBackward()"
        @keypress.enter.prevent.stop.self="addPointerElement($event)"
        :disabled="disabled"
        :placeholder="placeholder"
      >

      <div
        class="dropdown__button"
        :class="{'disabled' : disabled}"
        @mousedown.prevent.stop="toggle()"
      >
        <i class="fas fa-angle-down" aria-hidden="true"></i>
        <i class="fas fa-angle-up" aria-hidden="true"></i>
      </div>
    </div>

    <div
      class="dropdown__content"
      :class="{'dropdown__options__above': isAbove}"
      v-show="isActive"
      @focus="activate"
      tabindex="-1"
      @mousedown.prevent
    >
      <ul
        class="dropdown__options"
        :class="{'text-left': textAlign === 'left', 'text-right': textAlign === 'right'}"
        :style="{ maxHeight: optimizedHeight + 'px', 'min-width': optimizedWidth + 'px' }"
        ref="list"
      >
        <li
          class="dropdown__item"
          v-for="(option, index) in filteredOptions"
          :key="index"
          :class="optionHighlight(index, option)"
          @click.stop="optionSelected(option)"
          @mouseenter.self="pointerSet(index)"
        >{{ getOptionLabel(option) }}</li>
      </ul>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      keyword: "",
      maxHeight: this.optionHeight * 6,
      openDirection: "",
      optimizedHeight: this.maxHeight,
      optimizedWidth: 0,
      pointer: 0,
      preferredOpenDirection: "below",
      selectedLabel: this.getOptionLabel(this.value),
      selectedOption: this.value,
      isActive: false
    };
  },
  computed: {
    filteredOptions() {
      const keyword = this.keyword || "";
      const normalizedKeyword = keyword.toLowerCase().trim();

      let options = this.options.concat();

      options = options.filter(option => {
        const text = this.label ? option[this.label] : option;
        return (
          text
            .toString()
            .toLowerCase()
            .indexOf(normalizedKeyword) !== -1
        );
      });

      return options;
    },
    isAbove() {
      if (this.openDirection === "above" || this.openDirection === "top") {
        return true;
      } else if (
        this.openDirection === "below" ||
        this.openDirection === "bottom"
      ) {
        return false;
      } else {
        return this.preferredOpenDirection === "above";
      }
    },
    pointerPosition() {
      return this.pointer * this.optionHeight;
    },
    visibleElements() {
      return this.optimizedHeight / this.optionHeight;
    }
  },
  methods: {
    activate() {
      if (this.isActive || this.disabled) return;

      this.checkPosition();
      if (this.filteredOptions.length) {
        this.pointer = 0;
      }
      this.isActive = true;
      this.$nextTick(() => this.$refs.dropdownInput.focus());
      this.$refs.dropdownInput.select();
    },
    deactivate() {
      if (!this.isActive || this.disabled) return;

      this.isActive = false;
      this.$refs.dropdownInput.blur();
    },
    optionHighlight(index, option) {
      return {
        "is-selected": this.selectedLabel === this.getOptionLabel(option),
        "is-highlighted": index === this.pointer
      };
    },
    toggle() {
      this.isActive ? this.deactivate() : this.activate();
    },
    updateKeyword(keyword) {
      this.keyword = keyword;
    },
    optionSelected(option) {
      this.selectedLabel = this.resetAfter ? "" : this.getOptionLabel(option);
      this.selectedOption = option;
      this.$refs.dropdownInput.blur();

      this.$emit("input", option, this.value);
    },
    checkPosition() {
      if (typeof window === "undefined") return;

      const spaceAbove = this.$el.getBoundingClientRect().top;
      const spaceBelow =
        window.innerHeight - this.$el.getBoundingClientRect().bottom;
      const hasEnoughSpaceBelow = spaceBelow > this.maxHeight;

      if (
        hasEnoughSpaceBelow ||
        spaceBelow > spaceAbove ||
        this.openDirection === "below" ||
        this.openDirection === "bottom"
      ) {
        this.preferredOpenDirection = "below";
        this.optimizedHeight = Math.min(
          spaceBelow - this.optionHeight,
          this.maxHeight
        );
      } else {
        this.preferredOpenDirection = "above";
        this.optimizedHeight = Math.min(
          spaceAbove - this.optionHeight,
          this.maxHeight
        );
      }
    },
    getOptionLabel(option) {
      if (option == null) return "";

      return this.label ? option[this.label] : option;
    },
    pointerBackward() {
      if (this.pointer > 0) {
        this.pointer--;
        if (this.$refs.list.scrollTop >= this.pointerPosition) {
          this.$refs.list.scrollTop = this.pointerPosition;
        }
      }
    },
    pointerForward() {
      if (this.pointer < this.filteredOptions.length - 1) {
        this.pointer++;
        if (
          this.$refs.list.scrollTop <=
          this.pointerPosition - (this.visibleElements - 1) * this.optionHeight
        ) {
          this.$refs.list.scrollTop =
            this.pointerPosition -
            (this.visibleElements - 1) * this.optionHeight;
        }
      }
    },
    addPointerElement({ key } = "Enter") {
      if (this.filteredOptions.length > 0) {
        this.optionSelected(this.filteredOptions[this.pointer]);
      }
    },
    pointerSet(index) {
      this.pointer = index;
      this.pointerDirty = true;
    }
  },
  props: {
    disabled: {
      type: Boolean,
      default: false
    },
    label: {
      type: String
    },
    optionHeight: {
      type: Number,
      default: 32
    },
    options: {
      type: Array,
      required: true
    },
    placeholder: {
      type: String,
      default: "Select an option"
    },
    resetAfter: {
      type: Boolean,
      default: false
    },
    showPointer: {
      type: Boolean,
      default: true
    },
    textAlign: {
      type: String,
      default: "left"
    },
    trackBy: {
      type: String
    },
    value: {
      type: null,
      default() {
        return [];
      }
    }
  },
  mounted() {
    this.optimizedWidth = this.$refs.dropdownInput.clientWidth;
  }
};
</script>
<style lang="scss">
.dropdown {
  &__header {
    input[disabled] {
      color: #e2e8f0;
    }

    .dropdown__box {
      border: 2px solid #e2e8f0;
      padding: 0.5rem 32px 0.5rem 0.5rem;
      width: 100%;
    }

    .dropdown__button {
      position: absolute;
      right: 3px;
      top: 50%;
      transform: translateY(-50%);
      height: 30px;
      width: 24px;

      &:hover {
        background-color: #ebf8ff;
      }

      &.disabled {
        color: #e2e8f0;

        &:hover {
          background-color: transparent;
        }
      }

      i.fas {
        position: absolute;
        right: 7px;
        top: 50%;
        transform: translateY(-50%);
        transition: opacity 0.3s;

        &.fa-angle-up {
          opacity: 0;
        }
      }
    }

    &.is-active {
      .dropdown__button {
        i.fas {
          &.fa-angle-up {
            opacity: 1;
          }

          &.fa-angle-down {
            opacity: 0;
          }
        }
      }
    }
  }

  &__content {
    background: #ffffff;
    border: 2px solid #e2e8f0;
    box-shadow: 0 10px 16px 0 rgba(0, 0, 0, 0.2),
      0 6px 20px 0 rgba(0, 0, 0, 0.19) !important;
    height: auto;
    margin-top: 2px;
    opacity: 1;
    overflow: hidden;
    position: absolute;
    transition: opacity 0.3s;
    visibility: visible;
    width: auto;
    z-index: 61771;

    &.dropdown__options__above {
      bottom: 100%;
      box-shadow: 0 -6px 16px 0 rgba(0, 0, 0, 0.2),
        0 -2px 20px 0 rgba(0, 0, 0, 0.19) !important;
      margin-bottom: 2px;
    }

    .dropdown__options {
      list-style: none;
      margin: 0px;
      max-height: 240px;
      overflow-y: scroll;
      padding: 0px;

      .dropdown__item {
        cursor: pointer;
        padding: 4px 16px 4px 8px;
        white-space: nowrap;

        &:hover {
          background-color: #ebf8ff;
        }
        &.is-highlighted {
          background-color: #ebf8ff;
        }
        &.is-selected {
          background-color: #63b3ed;
          color: #ffffff;
        }
      }

      &.text-left {
        text-align: left;
      }

      &.text-right {
        text-align: right;
      }
    }
  }
}
</style>
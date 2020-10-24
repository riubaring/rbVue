<template>
  <transition>
    <div
      class="rb-modal-mask"
      :class="{'rb-modal-vertical-align-middle': verticalAlign !== 'top',
               'rb-modal-vertical-align-top': verticalAlign === 'top'}"
    >
      <div class="rb-modal-wrapper">
        <div class="rb-modal-container">
          <div class="rb-modal-header">
            <h5 class="rb-modal-title">
              <slot name="title">default title</slot>
            </h5>
            <button type="button" class="rb-modal-close" aria-label="Close" @click="$emit('close')">
              <span aria-hidden="true">×</span>
            </button>
          </div>

          <div class="rb-modal-body">
            <slot name="body">default body</slot>
          </div>

          <div class="rb-modal-footer">
            <slot name="footer">
              <button class="rb-modal-default-button" @click="$emit('close')">OK</button>
            </slot>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>
<script>
export default {
  props: {
    verticalAlign: {
      type: String,
      default: "middle"
    }
  }
};
</script>
<style lang="scss" scoped>
.rb-modal-mask {
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;

  .rb-modal-wrapper {
    display: table-cell;
  }

  &.rb-modal-vertical-align-middle {
    position: fixed;

    .rb-modal-wrapper {
      vertical-align: middle;
    }
  }

  &.rb-modal-vertical-align-top {
    position: absolute;

    .rb-modal-wrapper {
      vertical-align: top;
    }
  }
}

.rb-modal-container {
  width: 640px;
  margin: 0px auto;
  /*padding: 20px 30px;*/
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.rb-modal-header {
  display: -ms-flexbox;
  display: flex;
  -ms-flex-align: start;
  align-items: flex-start;
  -ms-flex-pack: justify;
  justify-content: space-between;
  padding: 1rem 1rem;
  border-bottom: 1px solid #dee2e6;
  border-top-left-radius: calc(0.3rem - 1px);
  border-top-right-radius: calc(0.3rem - 1px);
}

.rb-modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.rb-modal-title {
  line-height: 1.5;
  margin-bottom: 0;
  font-size: 1.25em;
}

.rb-modal-close {
  padding: 1rem 1rem;
  margin: -1rem -1rem -1rem auto;
  float: right;
  font-size: 1.5em;
  font-weight: 700;
  line-height: 1;
  color: #000;
  text-shadow: 0 1px 0 #fff;
  opacity: 0.5;
}

.rb-modal-body {
  position: relative;
  -ms-flex: 1 1 auto;
  flex: 1 1 auto;
  padding: 1rem;
}

.rb-modal-footer {
  display: -ms-flexbox;
  display: flex;
  -ms-flex-wrap: wrap;
  flex-wrap: wrap;
  -ms-flex-align: center;
  align-items: center;
  -ms-flex-pack: end;
  justify-content: flex-end;
  padding: 1.75rem;
  border-top: 1px solid #dee2e6;
  border-bottom-right-radius: calc(0.3rem - 1px);
  border-bottom-left-radius: calc(0.3rem - 1px);
}

.rb-modal-default-button {
  float: right;
}

.rb-modal-enter {
  opacity: 0;
}

.rb-modal-leave-active {
  opacity: 0;
}

.rb-modal-enter .rb-modal-container,
.rb-modal-leave-active .rb-modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>
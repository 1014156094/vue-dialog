<template>
  <div class="popup-container">
    <transition name="dialog-mask">
      <div
        class="mask"
        @click="onMask"
        v-show="show"
      />
    </transition>
    <transition name="dialog-bounce">
      <slot>
        <div
          class="dialog-container"
          v-show="show"
        >
          <i
            v-if="showCloseButton"
            class="icon-close"
            @click="handleAction('close')"
          />

          <div
            class="dialog-title"
            v-if="title || $slots.title"
          >
            <slot name="title">
              {{ title }}
            </slot>
          </div>

          <div
            class="dialog-message-container"
            v-if="message || $slots.message"
          >
            <slot name="message">
              <div v-html="message" />
            </slot>
          </div>

          <div
            class="dialog-buttons-container"
            v-if="showCancelButton || showConfirmButton || $slots.buttons"
          >
            <slot name="buttons">
              <a
                class="button cancel"
                @click="handleAction('cancel')"
                v-if="showCancelButton"
              >
                {{ cancelButtonText }}
              </a>
              <a
                class="button confirm"
                @click="handleAction('confirm')"
                v-if="showConfirmButton"
              >
                {{ confirmButtonText }}
              </a>
            </slot>
          </div>
        </div>
      </slot>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'BaseDialog',
  model: {
    prop: 'value',
    event: 'change'
  },
  props: {
    // v-model值
    value: {
      default: false,
      type: Boolean
    },
    // 点击蒙层时是否关闭弹窗
    closeOnClickOverlay: {
      default: false,
      type: Boolean
    },
    // 显示关闭按钮
    showCloseButton: {
      default: true,
      type: Boolean
    },
    // 显示取消按钮
    showCancelButton: {
      default: false,
      type: Boolean
    },
    // 显示确定按钮
    showConfirmButton: {
      default: true,
      type: Boolean
    },
    // 标题
    title: {
      default: '',
      type: String
    },
    // 消息
    message: {
      default: '',
      type: String
    },
    // 取消按钮文案
    cancelButtonText: {
      default: '取消',
      type: String
    },
    // 确定按钮文案
    confirmButtonText: {
      default: '确定',
      type: String
    },
    // 关闭之前做操作
    beforeClose: {
      default: null,
      type: Function
    }
  },
  watch: {
    value (newVal) {
      this.show = newVal
    }
  },
  data () {
    return {
      show: this.value // 可见状态
    }
  },
  methods: {
    // 遮罩
    onMask () {
      if (this.closeOnClickOverlay === true) {
        this.onClose()
      }
    },
    // 操作动作处理
    handleAction (action) {
      if (this.beforeClose) {
        /**
         * action [String] 操作动作，目前有cancel或confirm
         * state [Any] 只要为真就执行关闭
         */
        this.beforeClose(action, state => {
          if (state !== false) {
            this.onClose(action)
          }
        })
      } else {
        this.onClose(action)
      }
    },
    // 关闭
    onClose (action) {
      this.show = false
      this.$emit('change', this.show)
      this.$emit(action)
    }
  }
}
</script>

<style lang="less" scoped>
.popup-container {
  display: flex;
  justify-content: center;
  align-items: center;
  .mask {
    position: fixed;
    left: 0;
    top: 0;
    z-index: 2001;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.7);
  }

  .dialog-container {
    flex: none;
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    margin: auto;
    z-index: 2001;
    display: table;
    width: 85%;
    border-radius: 10px;
    box-sizing: border-box;
    font-size: 13px;
    line-height: initial;
    background: #fff;

    .icon-close {
      position: absolute;
      right: 20px;
      top: 20px;
      width: 20px;
      height: 20px;
      line-height: 20px;
      border-radius: 50%;
      text-align: center;
      font-size: 16px;
      font-weight: bold;
      background: url(./icon-close.png) center center / contain no-repeat;
    }

    .dialog-title {
      font-size: 16px;
      color: #333;
      text-align: center;
      font-weight: 500;
      margin: 20px;
    }

    .dialog-message-container {
      font-size: 14px;
      color: #333;
      line-height: 24px;
      margin: 10px 20px;
      text-align: center;
    }

    .dialog-buttons-container {
      display: flex;
      text-align: center;
      margin: 20px 10px;

      .button {
        flex: 1;
        height: 34px;
        line-height: 34px;
        margin: 0 10px;
        font-size: 16px;
        background: #449afd;
        border: 1px solid #449afd;
        color: #fff;
        border-radius: 18px;
        text-align: center;

        &.cancel {
          background: transparent;
          color: #999;
          border: 1px solid #999;
        }
      }
    }
  }
  .dialog-mask-enter-active {
    transition: all 0.3s;
  }
  .dialog-mask-leave-active {
    transition: all 0.3s;
  }
  .dialog-mask-enter,
  .dialog-mask-leave-to {
    opacity: 0;
  }
  .dialog-bounce-enter-active {
    transition: all 0.3s;
  }
  .dialog-bounce-leave-active {
    transition: all 0.3s;
  }
  .dialog-bounce-enter,
  .dialog-bounce-leave-to {
    transform: scale(0.7);
    opacity: 0;
  }
}
</style>

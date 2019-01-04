# vue-dialog

{% asset_img cover.jpg 基于 Vue 的对话框组件 %}

基础组件-对话框，可拷贝到项目里，调整下样式，可满足各种奇葩需求和设计。

<!--More-->
由于公司项目多、设计的多样性，比如右上角要有关闭按钮，弹窗要有2个按钮或3个按钮或封面或图标等等需求设计，像Van、Vux之类使用人数多UI库，是可以满足，但是要自己在它的对话框组件上再封装一层来复用右上角关闭按钮或底部按钮或其它样式，显得太繁琐，所以干脆自己写个定制性强的基础组件出来，用来应复多样性的项目，如果有新的外包项目、新的需求或设计，在这组件代码基础上 `增删改` 即可。

## 最终效果图
{% asset_img demo.gif 演示 %}

## 注意
组件CSS使用 `less` 预编译语言

## 插槽
| 参数 | 说明 |
| - | - |
| 默认 | 对话框主体 |
| title | 标题 |
| message | 消息 |
| buttons | 底部按钮 |

## Props
| 参数 | 说明 | 类型 | 默认值 |
| - | - | - | - |
| v-model | 是否显示对话框 | Boolean | false |
| title | 标题 | String | - |
| message | 消息 | String | - |
| show-close-button | 是否显示关闭按钮 | Boolean | true |
| show-cancel-button | 是否显示取消按钮 | Boolean | false |
| show-confirm-button | 是否显示确定按钮 | Boolean | true |
| cancel-button-text | 取消按钮文案 | String | 确定 |
| confirm-button-text | 确定按钮文案 | String | 取消 |
| close-on-click-overlay | 是否在点击蒙层后关闭 | Boolean | false |
| before-close |关闭前的回调函数 <br> 调用 done() 后关闭弹窗 <br> 调用 done(false) 阻止弹窗关闭|(action, done) => void | - |

```
<template>
  <div class="popup-container">
    <transition name="dialog-mask">
      <div class="mask"
           @click="onMask"
           v-show="show" />
    </transition>
    <transition name="dialog-bounce">
      <slot>
        <div class="dialog-container"
             v-show="show">
          <i v-if="showCloseButton"
             class="iconfont icon-close"
             @click="handleAction('close')" />

          <div class="dialog-title"
               v-if="title || $slots.title">
            <slot name="title">
              {{ title }}
            </slot>
          </div>

          <div class="dialog-message-container"
               v-if="message || $slots.message">
            <slot name="message">
              <div v-html="message" />
            </slot>
          </div>

          <div class="dialog-buttons-container"
               v-if="showCancelButton || showConfirmButton || $slots.buttons">
            <slot name="buttons">
              <a class="button cancel"
                 @click="handleAction('cancel')"
                 v-if="showCancelButton">
                {{ cancelButtonText }}
              </a>
              <a class="button confirm"
                 @click="handleAction('confirm')"
                 v-if="showConfirmButton">
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
  name: 'popup-dialog',
  model: {
    prop: 'value',
    event: 'change'
  },
  props: {
    // v-model值
    value: {
      required: true,
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
      default: true,
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
      line-height: 20px;
      border-radius: 50%;
      text-align: center;
      font-size: 16px;
      font-weight: bold;
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
```

## 二次封装基础组件-对话框示例
```
<template>
  <base-dialog class="dialog-address"
                v-model="show"
                title="温馨提示"
                cancelButtonText="再考虑下"
                :beforeClose="beforeCloseExchange">
    <template slot="message">
      <input type="text"
             placeholder="请填写收货地址"
             v-model="exchangeAddress" />
    </template>
  </base-dialog>
</template>

<script>
import BaseDialog from '@/components/base-dialog'

export default {
  mixins: [BaseDialog],
  data () {
    return {
      exchangeAddress: '' // 兑换地址
    }
  },
  watch: {
    show (newVal) {
      this.$emit('change', this.show)
    }
  },
  methods: {
    beforeCloseExchange (action, done) {
      if (action === 'confirm') {
        if (!this.exchangeAddress.trim() || !this.exchangeAddress) {
          this.$toast('请填写兑换地址！')
          return
        }
        this.$emit('confirmSuccess', {
          exchangeAddress: this.exchangeAddress
        })
      }
      done()
    }
  }
}
</script>
```

<template>
  <div>
    <button @click="showDialog = true">
      显示对话框
    </button>
    <BaseDialog
      v-model="showDialog"
      title="标题"
      message="内容"
      :show-cancel-button="true"
      :show-close-button="true"
    />
  </div>
</template>

<script>
import BaseDialog from '@/components/base-dialog'

export default {
  components: {
    BaseDialog
  },
  data () {
    return {
      showDialog: false
    }
  }
}
</script>

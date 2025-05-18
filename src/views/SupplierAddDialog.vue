<template>
  <el-dialog
    v-model="visibleValue"
    title="供应商添加"
    width="380px"
    :close-on-click-modal="false"
    class="supplier-add-dialog"
    @close="cancel"
  >
    <el-form
      ref="formRef"
      :model="form"
      :rules="rules"
      label-width="120px"
      class="supplier-add-form"
    >
      <el-form-item label="供应名称" prop="name">
        <el-input v-model="form.name" placeholder="请输入供应名称" clearable />
      </el-form-item>
      <el-form-item label="供应返点比例" prop="rate">
        <el-input 
          v-model="form.rate" 
          placeholder="请输入返点比例，如0.1-1" 
          clearable 
          type="number"
          step="0.01"
          min="0"
          max="1"
          @input="handleRateInput"
        />
      </el-form-item>
    </el-form>
    <div class="supplier-add-btns">
      <el-button @click="$emit('update:visible', false)" class="btn-cancel">取消</el-button>
      <el-button type="success" @click="onSubmit" class="btn-confirm">添加</el-button>
    </div>
  </el-dialog>
</template>

<script setup>
import { ref, reactive, watch } from 'vue'
const props = defineProps({
  visible: Boolean
})
const emits = defineEmits(['update:visible', 'submit'])
const visibleValue = ref(props.visible)
watch(() => props.visible, v => visibleValue.value = v)
watch(visibleValue, v => emits('update:visible', v))

const formRef = ref()
const form = reactive({
  name: '',
  rate: ''
})
const rules = reactive({
  name: [{ required: true, message: '请输入供应名称', trigger: 'blur' }],
  rate: [
    { required: true, message: '请输入返点比例', trigger: 'blur' },
    { 
      validator: (rule, value, callback) => {
        if (value === '') {
          callback()
          return
        }
        const num = parseFloat(value)
        if (isNaN(num)) {
          callback(new Error('请输入有效的数字'))
          return
        }
        if (num < 0 || num > 1) {
          callback(new Error('返点比例必须在0-1之间'))
          return
        }
        if (!/^\d+(\.\d{1,2})?$/.test(value)) {
          callback(new Error('最多保留2位小数'))
          return
        }
        callback()
      },
      trigger: 'blur'
    }
  ]
})
const cancel = () => {
  formRef.value.resetFields()
  emits('update:visible', false)
}
const onSubmit = () => {
  formRef.value.validate((valid) => {
    if (valid) {
      emits('submit', { ...form })
      formRef.value.resetFields()
    }
  })
}
const handleRateInput = (value) => {
  if (value) {
    // 限制小数位数为2位
    const num = parseFloat(value)
    if (!isNaN(num)) {
      form.rate = Number(num.toFixed(2))
    }
  }
}
</script>

<style scoped>
.supplier-add-dialog >>> .el-dialog__header {
  border-bottom: 1px solid #f0f0f0;
}
.supplier-add-form {
  margin-top: 10px;
}
.supplier-add-btns {
  display: flex;
  justify-content: flex-end;
  gap: 12px;
  margin-top: 18px;
}
.btn-cancel {
  background: #f5f5f5;
  color: #666;
}
.btn-confirm {
  background: #67c23a;
  color: #fff;
}
</style> 
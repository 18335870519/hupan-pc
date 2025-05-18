<template>
  <el-dialog
    v-model="visibleValue"
    title="营业配置"
    width="380px"
    :close-on-click-modal="false"
    class="business-config-dialog"
    @close="cancel"
  >
    <el-form
      ref="formRef"
      :model="form"
      :rules="rules"
      label-width="110px"
      class="business-config-form"
    >
      <el-form-item label="营业状态" prop="status">
        <el-radio-group v-model="form.status">
          <el-radio label="开店">开店</el-radio>
          <el-radio label="关店">关店</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="打烊时间" prop="closeTime">
        <el-select v-model="form.closeTime" placeholder="请选择打烊时间">
          <el-option
            v-for="time in closeTimeOptions"
            :key="time"
            :label="time"
            :value="time"
          />
        </el-select>
      </el-form-item>
      <el-form-item label="打包费" prop="pack">
        <el-input v-model="form.pack" placeholder="请输入打包费" clearable type="number" min="0" />
      </el-form-item>
      <el-form-item label="配送费" prop="delivery">
        <el-input v-model="form.delivery" placeholder="请输入配送费" clearable type="number" min="0" />
      </el-form-item>
      <el-form-item label="起送金额" prop="start">
        <el-input v-model="form.start" placeholder="请输入起送金额" clearable type="number" min="0" />
      </el-form-item>
      <el-form-item label="0配送费标准" prop="free">
        <el-input v-model="form.free" placeholder="请输入0配送费标准" clearable type="number" min="0" />
      </el-form-item>
      <el-form-item label="履约时间标准" prop="promise">
        <el-select v-model="form.promise" placeholder="请选择履约时间标准">
          <el-option
            v-for="min in promiseOptions"
            :key="min"
            :label="min + '分钟'"
            :value="min + '分钟'"
          />
        </el-select>
      </el-form-item>
    </el-form>
    <div class="business-config-btns">
      <el-button @click="cancel" class="btn-cancel">取消</el-button>
      <el-button type="success" @click="onSubmit" class="btn-confirm">确认变更</el-button>
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
  status: '',
  closeTime: '',
  pack: '',
  delivery: '',
  start: '',
  free: '',
  promise: ''
})
const rules = reactive({
  status: [{ required: false, message: '请选择营业状态', trigger: 'change' }],
  closeTime: [{ required: false, message: '请选择打烊时间', trigger: 'change' }],
  pack: [
    { required: true, message: '请输入打包费', trigger: 'blur' },
    { type: 'number', min: 0, message: '打包费必须大于0', trigger: 'blur' }
  ],
  delivery: [
    { required: true, message: '请输入配送费', trigger: 'blur' },
    { type: 'number', min: 0, message: '配送费必须大于0', trigger: 'blur' }
  ],
  start: [
    { required: true, message: '请输入起送金额', trigger: 'blur' },
    { type: 'number', min: 0, message: '起送金额必须大于0', trigger: 'blur' }
  ],
  free: [
    { required: true, message: '请输入0配送费标准', trigger: 'blur' },
    { type: 'number', min: 0, message: '0配送费标准必须大于0', trigger: 'blur' }
  ],
  promise: [{ required: false, message: '请选择履约时间标准', trigger: 'change' }]
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

const closeTimeOptions = Array.from({ length: 24 * 4 }, (_, i) => {
  const h = String(Math.floor(i / 4)).padStart(2, '0')
  const m = String((i % 4) * 15).padStart(2, '0')
  return `${h}:${m}`
})

const promiseOptions = Array.from({ length: ((60 - 15) / 5 + 1) }, (_, i) => 15 + i * 5)
</script>

<style scoped>
.business-config-dialog >>> .el-dialog__header {
  border-bottom: 1px solid #f0f0f0;
}
.business-config-form {
  margin-top: 10px;
}
.business-config-btns {
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
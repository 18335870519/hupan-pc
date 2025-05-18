<template>
  <el-dialog
    v-model="visibleValue"
    title="封号管理"
    width="420px"
    :close-on-click-modal="false"
    class="ban-dialog"
    @close="cancel"
  >
    <el-form
      ref="formRef"
      :model="form"
      :rules="rules"
      label-width="110px"
      class="ban-form"
      size="default"
    >
      <el-form-item label="30天封号名单" prop="ban30">
        <el-input v-model="form.ban30" placeholder="请输入30天封号用户（可多个，逗号分隔）" clearable />
      </el-form-item>
      <el-form-item label="永久封号名单" prop="banForever">
        <el-input v-model="form.banForever" placeholder="请输入永久封号用户（可多个，逗号分隔）" clearable />
      </el-form-item>
      <el-form-item label="封号原因" prop="reason">
        <el-select v-model="form.reason" placeholder="请选择封号原因" clearable>
          <el-option label="恶意索赔" value="恶意索赔" />
          <el-option label="恶意下单" value="恶意下单" />
        </el-select>
      </el-form-item>
    </el-form>
    <div class="ban-btns">
      <el-button @click="cancel" class="btn-cancel">取消</el-button>
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
  ban30: '',
  banForever: '',
  reason: ''
})
const rules = reactive({
  ban30: [],
  banForever: [],
  reason: [{ required: true, message: '请输入封号原因', trigger: 'blur' }]
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
</script>

<style scoped>
.ban-dialog >>> .el-dialog__header {
  border-bottom: 1px solid #f0f0f0;
}
.ban-form {
  margin-top: 10px;
}
.ban-btns {
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
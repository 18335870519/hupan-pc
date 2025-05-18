<template>
  <el-dialog
    v-model="visibleValue"
    title="新增员工"
    width="400px"
    :close-on-click-modal="false"
    class="add-staff-dialog"
    @close="cancel"
  >
    <el-form
      ref="formRef"
      :model="form"
      :rules="rules"
      label-width="90px"
      class="add-staff-form"
    >
      <el-form-item label="员工姓名" prop="name">
        <el-input v-model="form.name" placeholder="请输入员工姓名" clearable />
      </el-form-item>
      <el-form-item label="员工电话" prop="phone">
        <el-input v-model="form.phone" placeholder="请输入员工电话" clearable />
      </el-form-item>
      <el-form-item label="归属主体" prop="owner">
        <el-select v-model="form.owner" placeholder="请选择归属主体">
          <el-option label="佳合供应" value="佳合供应" />
          <el-option label="门店A" value="门店A" />
          <el-option label="门店B" value="门店B" />
        </el-select>
      </el-form-item>
      <el-form-item label="角色" prop="role">
        <el-select v-model="form.role" placeholder="请选择角色">
          <el-option label="管理员" value="管理员" />
          <el-option label="店员" value="店员" />
        </el-select>
      </el-form-item>
    </el-form>
    <div class="add-staff-btns">
      <el-button @click="cancel" class="btn-cancel">取消</el-button>
      <el-button type="primary" @click="onSubmit" class="btn-confirm">添加</el-button>
    </div>
  </el-dialog>
</template>

<script setup>
import { ref, reactive, watch } from 'vue'
import { ElMessage } from 'element-plus'
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
  phone: '',
  owner: '',
  role: ''
})
const rules = reactive({
  name: [{ required: true, message: '请输入员工姓名', trigger: 'blur' }],
  phone: [
    { required: true, message: '请输入员工电话', trigger: 'blur' },
    { pattern: /^1[3-9]\d{9}$/, message: '请输入正确的手机号', trigger: 'blur' }
  ],
  owner: [{ required: true, message: '请选择归属主体', trigger: 'change' }],
  role: [{ required: true, message: '请选择角色', trigger: 'change' }]
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
.add-staff-dialog >>> .el-dialog__header {
  border-bottom: 1px solid #f0f0f0;
}
.add-staff-form {
  margin-top: 10px;
}
.add-staff-btns {
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
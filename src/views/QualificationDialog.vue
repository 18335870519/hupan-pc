<template>
  <el-dialog
    v-model="visibleValue"
    title="供应商资质信息"
    width="520px"
    :close-on-click-modal="false"
    class="qualification-dialog"
    @close="$emit('close')"
  >
    <div class="qualification-form-bg">
      <el-form
        ref="formRef"
        :model="form"
        :rules="rules"
        label-width="100px"
        class="qualification-form"
      >
        <el-form-item label="经营者姓名" prop="name">
          <el-input v-model="form.name" clearable placeholder="请输入姓名" />
        </el-form-item>
        <el-form-item label="手机号" prop="phone">
          <el-input v-model="form.phone" clearable placeholder="请输入手机号" />
        </el-form-item>
        <el-form-item label="身份证照片">
          <div class="id-upload-row">
            <el-upload
              class="id-upload-block"
              action="#"
              :show-file-list="false"
              :on-change="file => handleUpload('idFront', file)"
              :before-upload="beforeImageUpload"
              accept=".jpg,.jpeg,.png,.webp"
            >
              <span class="id-label">正面</span>
              <el-image
                v-if="form.idFront"
                :src="form.idFront"
                style="width: 60px; height: 60px; margin-bottom: 4px; border-radius: 4px;"
                fit="cover"
                :preview-src-list="[form.idFront]"
              />
              <el-button size="small" type="primary" plain>上传</el-button>
            </el-upload>
            <el-upload
              class="id-upload-block"
              action="#"
              :show-file-list="false"
              :on-change="file => handleUpload('idBack', file)"
              :before-upload="beforeImageUpload"
              accept=".jpg,.jpeg,.png,.webp"
            >
              <span class="id-label">反面</span>
              <el-image
                v-if="form.idBack"
                :src="form.idBack"
                style="width: 60px; height: 60px; margin-bottom: 4px; border-radius: 4px;"
                fit="cover"
                :preview-src-list="[form.idBack]"
              />
              <el-button size="small" type="primary" plain>上传</el-button>
            </el-upload>
          </div>
        </el-form-item>
        <el-form-item label="供应商名称" prop="supplierName">
          <el-input disabled=true v-model="form.supplierName" clearable placeholder="请输入供应商名称" />
        </el-form-item>
        <el-form-item label="供应地址" prop="supplierAddress">
          <el-input v-model="form.supplierAddress" clearable placeholder="请输入供应地址" />
        </el-form-item>
        <el-form-item label="供应类型" prop="supplierType">
          <el-radio-group v-model="form.supplierType">
            <el-radio label="内供">内供</el-radio>
            <el-radio label="外供">外供</el-radio>
          </el-radio-group>
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
        <el-form-item label="营业执照" prop="license">
          <el-upload
            action="#"
            :show-file-list="false"
            :on-change="file => handleUpload('license', file)"
            :before-upload="beforeImageUpload"
            accept=".jpg,.jpeg,.png,.webp"
          >
            <el-image
              v-if="form.license"
              :src="form.license"
              style="width: 60px; height: 60px; margin-bottom: 4px; border-radius: 4px;"
              fit="cover"
              :preview-src-list="[form.license]"
            />
            <el-button size="small" type="success" plain>上传</el-button>
          </el-upload>
        </el-form-item>
        <el-form-item label="食品许可证" prop="foodPermit">
          <el-upload
            action="#"
            :show-file-list="false"
            :on-change="file => handleUpload('foodPermit', file)"
            :before-upload="beforeImageUpload"
            accept=".jpg,.jpeg,.png,.webp"
          >
            <el-image
              v-if="form.foodPermit"
              :src="form.foodPermit"
              style="width: 60px; height: 60px; margin-bottom: 4px; border-radius: 4px;"
              fit="cover"
              :preview-src-list="[form.foodPermit]"
            />
            <el-button size="small" type="success" plain>上传</el-button>
          </el-upload>
        </el-form-item>
        <el-form-item label="健康证" prop="healthPermit">
          <el-upload
            action="#"
            :show-file-list="false"
            :on-change="file => handleUpload('healthPermit', file)"
            :before-upload="beforeImageUpload"
            accept=".jpg,.jpeg,.png,.webp"
          >
            <el-image
              v-if="form.healthPermit"
              :src="form.healthPermit"
              style="width: 60px; height: 60px; margin-bottom: 4px; border-radius: 4px;"
              fit="cover"
              :preview-src-list="[form.healthPermit]"
            />
            <el-button size="small" type="success" plain>上传</el-button>
          </el-upload>
        </el-form-item>
      </el-form>
      <div class="qualification-btns">
        <el-button @click="$emit('close')" class="btn-cancel">取消</el-button>
        <el-button type="primary" @click="onSubmit" class="btn-confirm">确认修改</el-button>
      </div>
    </div>
  </el-dialog>
</template>

<script setup>
import { ref, watch, reactive } from 'vue'
import { ElMessage } from 'element-plus'
const props = defineProps({
  visible: Boolean,
  form: Object
})
const emits = defineEmits(['update:visible', 'close', 'submit'])
const visibleValue = ref(props.visible)
watch(() => props.visible, v => visibleValue.value = v)
watch(visibleValue, v => emits('update:visible', v))

const formRef = ref()

const rules = reactive({
  supplierAddress: [{ required: true, message: '请输入供应地址', trigger: 'blur' }],
  supplierType: [{ required: true, message: '请选择供应类型', trigger: 'change' }],
})

const beforeImageUpload = (file) => {
  const isImage = ['image/jpeg', 'image/png', 'image/webp', 'image/jpg'].includes(file.type)
  const isLt2M = file.size / 1024 / 1024 < 2
  if (!isImage) {
    ElMessage.error('仅支持jpg/png/webp格式图片')
    return false
  }
  if (!isLt2M) {
    ElMessage.error('图片大小不能超过2MB')
    return false
  }
  return true
}

const handleUpload = (field, file) => {
  // 真实项目用 file.response.url，这里用本地base64预览
  const raw = file.raw || (file && file.file && file.file.raw)
  if (!raw) return
  const reader = new FileReader()
  reader.onload = e => {
    props.form[field] = e.target.result
  }
  reader.readAsDataURL(raw)
  ElMessage.success('上传成功')
}

const onSubmit = () => {
  formRef.value.validate((valid) => {
    if (valid) {
      emits('submit')
    }
  })
}
</script>

<style scoped>
.qualification-dialog >>> .el-dialog__header {
  border-bottom: 1px solid #f0f0f0;
  padding-bottom: 8px;
}
.qualification-dialog >>> .el-dialog__body {
  background: #f9fafb;
  border-radius: 0 0 12px 12px;
  box-shadow: 0 2px 12px #0000000d;
  padding: 24px 28px 12px 28px;
}
.qualification-form-bg {
  background: transparent;
  border-radius: 12px;
}
.qualification-form {
  margin-bottom: 10px;
}
.el-form-item {
  margin-bottom: 18px;
}
.id-upload-row {
  display: flex;
  gap: 32px;
}
.id-upload-block {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 6px;
}
.id-label {
  font-size: 13px;
  color: #666;
  margin-bottom: 2px;
}
.qualification-btns {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  margin-top: 18px;
  gap: 18px;
}
.btn-cancel {
  background: #f5f6fa;
  color: #666;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
}
.btn-cancel:hover {
  background: #e6e8f0;
  color: #333;
}
.btn-confirm {
  background: linear-gradient(90deg, #4fc08d 0%, #38b2ff 100%);
  color: #fff;
  border: none;
  border-radius: 6px;
  font-weight: bold;
}
.btn-confirm:hover {
  background: linear-gradient(90deg, #38b2ff 0%, #4fc08d 100%);
  color: #fff;
}
</style>

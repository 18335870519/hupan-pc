<template>
  <el-dialog
    v-model="visibleValue"
    title="添加供应商商品"
    width="540px"
    :close-on-click-modal="false"
    class="add-supplier-dialog"
    @close="cancel"
  >
    <el-form
      ref="formRef"
      :model="form"
      :rules="rules"
      label-width="120px"
      class="add-supplier-form"
    >
      <el-form-item label="供应商" prop="supplier">
        <el-select v-model="form.supplier" placeholder="请选择供应商" clearable>
          <el-option v-for="item in supplierOptions" :key="item.value" :label="item.label" :value="item.value" />
        </el-select>
      </el-form-item>
      <el-form-item label="商品分类" required>
        <el-row :gutter="8">
          <el-col :span="8">
            <el-form-item prop="cat1" label-width="0">
              <el-select v-model="form.cat1" placeholder="一级分类" filterable>
                <el-option v-for="item in categoryOptions" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="cat2" label-width="0">
              <el-select v-model="form.cat2" placeholder="二级分类" :disabled="!form.cat1" filterable>
                <el-option v-for="item in cat2Options" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item prop="cat3" label-width="0">
              <el-select v-model="form.cat3" placeholder="三级分类" :disabled="!form.cat2" filterable>
                <el-option v-for="item in cat3Options" :key="item.value" :label="item.label" :value="item.value" />
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form-item>
      <el-form-item label="商品名称" prop="productName">
        <el-input v-model="form.productName" placeholder="请输入商品名称" clearable />
      </el-form-item>
      <el-form-item label="商品供价" prop="supplyPrice">
        <el-input v-model="form.supplyPrice" placeholder="请输入供货价" clearable type="number" />
      </el-form-item>
      <el-form-item label="商品售价" prop="salePrice">
        <el-input v-model="form.salePrice" placeholder="请输入销售价" clearable type="number" />
      </el-form-item>
      <el-form-item label="额外制作分钟" prop="extraTime">
        <el-input-number
          v-model="form.extraTime"
          :min="1"
          :step="1"
          placeholder="请输入时间(分钟)"
          style="width: 100%;"
          controls-position="right"
        />
      </el-form-item>
      <el-form-item label="商品图片" required>
        <div class="img-upload-row">
          <div class="img-upload-block">
            <span class="img-label">首页</span>
            <el-upload
              action="#"
              :show-file-list="false"
              :on-change="file => handleUpload('img1', file)"
              :before-upload="beforeImageUpload"
              accept=".jpg,.jpeg,.png,.webp"
            >
              <el-image
                v-if="form.img1"
                :src="form.img1"
                style="width: 60px; height: 60px; margin-bottom: 4px; border-radius: 4px;"
                fit="cover"
                :preview-src-list="[form.img1]"
              />
              <el-button size="small" type="primary" plain>上传</el-button>
            </el-upload>
          </div>
          <div class="img-upload-block">
            <span class="img-label">图1</span>
            <el-upload
              action="#"
              :show-file-list="false"
              :on-change="file => handleUpload('img2', file)"
              :before-upload="beforeImageUpload"
              accept=".jpg,.jpeg,.png,.webp"
            >
              <el-image
                v-if="form.img2"
                :src="form.img2"
                style="width: 60px; height: 60px; margin-bottom: 4px; border-radius: 4px;"
                fit="cover"
                :preview-src-list="[form.img2]"
              />
              <el-button size="small" type="primary" plain>上传</el-button>
            </el-upload>
          </div>
          <div class="img-upload-block">
            <span class="img-label">图2</span>
            <el-upload
              action="#"
              :show-file-list="false"
              :on-change="file => handleUpload('img3', file)"
              :before-upload="beforeImageUpload"
              accept=".jpg,.jpeg,.png,.webp"
            >
              <el-image
                v-if="form.img3"
                :src="form.img3"
                style="width: 60px; height: 60px; margin-bottom: 4px; border-radius: 4px;"
                fit="cover"
                :preview-src-list="[form.img3]"
              />
              <el-button size="small" type="primary" plain>上传</el-button>
            </el-upload>
          </div>
        </div>
      </el-form-item>
    </el-form>
    <div class="add-supplier-btns">
      <el-button @click="cancel" class="btn-cancel">取消</el-button>
      <el-button type="primary" @click="onSubmit" class="btn-confirm">确认添加</el-button>
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
  supplier: '',
  cat1: '',
  cat2: '',
  cat3: '',
  productName: '',
  supplyPrice: '',
  salePrice: '',
  extraTime: '',
  img1: '',
  img2: '',
  img3: ''
})
const rules = reactive({
  supplier: [{ required: true, message: '请输入供应商', trigger: 'blur' }],
  cat1: [{ required: true, message: '请选择一级分类', trigger: 'change' }],
  cat2: [{ required: true, message: '请选择二级分类', trigger: 'change' }],
  cat3: [{ required: true, message: '请选择三级分类', trigger: 'change' }],
  productName: [{ required: true, message: '请输入商品名称', trigger: 'blur' }],
  supplyPrice: [{ required: true, message: '请输入供货价', trigger: 'blur' }],
  salePrice: [{ required: true, message: '请输入销售价', trigger: 'blur' }],
  extraTime: [{ required: true, message: '请输入时间', trigger: 'blur' }],
  img1: [{ required: true, message: '请上传首页图片', trigger: 'change' }],
  img2: [{ required: true, message: '请上传默认1图片', trigger: 'change' }],
  img3: [{ required: true, message: '请上传默认2图片', trigger: 'change' }]
})
const supplierOptions = [
  { label: '杭州供应商A', value: 'S001' },
  { label: '上海供应商B', value: 'S002' },
  { label: '北京供应商C', value: 'S003' }
]
const categoryOptions = [
  { label: '食品', value: '食品' },
  { label: '日用品', value: '日用品' },
  { label: '餐饮', value: '餐饮' },
  { label: '服饰', value: '服饰' },
  { label: '五金', value: '五金' },
  { label: '电子文娱', value: '电子文娱' }
]
const cat2Map = {
  '食品': [
    { label: '零食', value: '零食' },
    { label: '饮品', value: '饮品' },
    { label: '生鲜', value: '生鲜' },
    { label: '粮油调料', value: '粮油调料' },
    { label: '保健品', value: '保健品' }
  ],
  '日用品': [
    { label: '厨品', value: '厨品' },
    { label: '洗护', value: '洗护' },
    { label: '清洁', value: '清洁' },
    { label: '家居', value: '家居' },
    { label: '文具', value: '文具' }
  ],
  '餐饮': [
    { label: '早餐', value: '早餐' },
    { label: '健康餐', value: '健康餐' },
    { label: '快餐', value: '快餐' },
    { label: '小炒', value: '小炒' },
    { label: '下午茶', value: '下午茶' },
    { label: '夜宵', value: '夜宵' }
  ],
  '服饰': [
    { label: '内搭', value: '内搭' },
    { label: '饰品', value: '饰品' },
    { label: '鞋履', value: '鞋履' },
    { label: '服装', value: '服装' },
    { label: '包袋', value: '包袋' }
  ],
  '五金': [
    { label: '交电类', value: '交电类' },
    { label: '金属类', value: '金属类' },
    { label: '机械类', value: '机械类' },
    { label: '电器工具', value: '电器工具' },
    { label: '建筑装饰', value: '建筑装饰' }
  ],
  '电子文娱': [
    { label: '潮玩', value: '潮玩' },
    { label: '运动', value: '运动' },
    { label: '配饰', value: '配饰' },
    { label: '工艺品', value: '工艺品' },
    { label: '5元专区', value: '5元专区' },
    { label: '电子设备', value: '电子设备' }
  ]
}
const cat3Map = {
  '零食': [
    { label: '肉制品', value: '肉制品' },
    { label: '糖制品', value: '糖制品' },
    { label: '膨化类', value: '膨化类' },
    { label: '果干类', value: '果干类' },
    { label: '素食品', value: '素食品' },
    { label: '速食类', value: '速食类' }
  ],
  '饮品': [
    { label: '酒', value: '酒' },
    { label: '水', value: '水' },
    { label: '果汁', value: '果汁' },
    { label: '碳酸饮料', value: '碳酸饮料' },
    { label: '功能饮料', value: '功能饮料' }
  ],
  '生鲜': [
    { label: '肉蛋', value: '肉蛋' },
    { label: '水产', value: '水产' },
    { label: '蔬菜', value: '蔬菜' },
    { label: '水果', value: '水果' },
    { label: '乳制品', value: '乳制品' }
  ],
  '粮油调料': [
    { label: '米面', value: '米面' },
    { label: '豆类', value: '豆类' },
    { label: '食用油', value: '食用油' },
    { label: '杂粮', value: '杂粮' },
    { label: '火锅料', value: '火锅料' },
    { label: '炒菜料', value: '炒菜料' }
  ],
  '厨品': [
    { label: '器皿', value: '器皿' },
    { label: '洗剂', value: '洗剂' }
  ],
  '洗护': [
    { label: '洗涤', value: '洗涤' },
    { label: '沐浴', value: '沐浴' },
    { label: '卫生品', value: '卫生品' }
  ],
  '清洁': [
    { label: '拖扫', value: '拖扫' },
    { label: '清洁剂', value: '清洁剂' }
  ],
  '家居': [
    { label: '储物', value: '储物' },
    { label: '床品', value: '床品' },
    { label: '小家电', value: '小家电' }
  ],
  '早餐': [
    { label: '蔬水', value: '蔬水' },
    { label: '蛋白', value: '蛋白' },
    { label: '饮品', value: '饮品' }
  ],
  '健康餐': [
    { label: '食材新鲜、少油少盐、拒绝化学添加、干净卫生', value: '食材新鲜、少油少盐、拒绝化学添加、干净卫生' },
  ],
  '快餐': [
    { label: '中式快餐', value: '中式快餐' },
    { label: '西式快餐', value: '西式快餐' }
  ],
  '小炒': [],
  '下午茶': [
    { label: '烘焙', value: '烘焙' },
    { label: '奶茶', value: '奶茶' },
    { label: '咖啡', value: '咖啡' },
    { label: '水果捞', value: '水果捞' }
  ],
  '夜宵': [
    { label: '卤味', value: '卤味' },
    { label: '烧烤', value: '烧烤' }
  ],
  '交电类': [
    { label: '锁具', value: '锁具' },
    { label: '门窗', value: '门窗' },
    { label: '家装', value: '家装' }
  ],
  '金属类': [
    { label: '大五金', value: '大五金' },
    { label: '小五金', value: '小五金' }
  ],
  '机械类': [
    { label: '机械用具', value: '机械用具' },
    { label: '零配件', value: '零配件' }
  ],
  '电器工具': [
    { label: '电器设备', value: '电器设备' },
    { label: '日用工具', value: '日用工具' },
    { label: '园艺工具', value: '园艺工具' }
  ],
  '建筑装饰': [
    { label: '管道类', value: '管道类' },
    { label: '防护设备', value: '防护设备' },
    { label: '装饰材料', value: '装饰材料' }
  ]
}
const cat2Options = ref([])
const cat3Options = ref([])
watch(() => form.cat1, (val) => {
  form.cat2 = ''
  form.cat3 = ''
  cat2Options.value = cat2Map[val] || []
  cat3Options.value = []
})

watch(() => form.cat2, (val) => {
  form.cat3 = ''
  cat3Options.value = cat3Map[val] || []
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
  const raw = file.raw || (file && file.file && file.file.raw)
  if (!raw) return
  const reader = new FileReader()
  reader.onload = e => {
    form[field] = e.target.result
  }
  reader.readAsDataURL(raw)
  ElMessage.success('上传成功')
}
const cancel = () => {
  formRef.value.resetFields()
  emits('update:visible', false)
}
const onSubmit = () => {
  formRef.value.validate((valid) => {
    if (valid) {
      emits('submit', { ...form })
      form.productName = ''
      form.supplyPrice = ''
      form.salePrice = ''
      form.extraTime = ''
      form.img1 = ''
      form.img2 = ''
      form.img3 = ''
    }
  })
}
</script>

<style scoped>
.add-supplier-dialog >>> .el-dialog__header {
  border-bottom: 1px solid #f0f0f0;
  padding-bottom: 8px;
}
.add-supplier-dialog >>> .el-dialog__body {
  background: #f9fafb;
  border-radius: 0 0 12px 12px;
  box-shadow: 0 2px 12px #0000000d;
  padding: 24px 28px 12px 28px;
}
.add-supplier-form {
  margin-bottom: 10px;
}
.el-form-item {
  margin-bottom: 18px;
}
.img-upload-row {
  display: flex;
  gap: 32px;
  align-items: center;
}
.img-upload-block {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 6px;
  min-width: 90px;
}
.img-label {
  font-size: 13px;
  color: #666;
  margin-bottom: 2px;
  white-space: nowrap;
}
.add-supplier-btns {
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

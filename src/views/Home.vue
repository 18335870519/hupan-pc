<template>
  <div class="home-container">
    <el-card>
      <template #header>
        <div class="card-header">
          <span>湖畔帮帮后台管理系统</span>
        </div>
      </template>
      <div class="card-content">
        <!-- 商店概览 -->
        <div class="table-block">
          <div class="table-title1">
            <span>商店概览</span>
            <el-date-picker
              v-model="dateRange"
              type="daterange"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
              format="YYYY.MM.DD"
              value-format="YYYY.MM.DD"
              size="small"
              style="width: 260px; margin: 0 16px;"
            />
            <el-button type="success" size="small" @click="handleOverviewQuery">查询</el-button>
          </div>
          <el-table :data="overviewData" border style="margin-bottom: 24px;">
            <el-table-column v-for="col in overviewColumns" :key="col.prop" :prop="col.prop" :label="col.label" align="center" :min-width="col.minWidth || '80px'" />
          </el-table>
        </div>
        <!-- 商店概览2 -->
        <div class="table-block">
          <div class="table-title">
            <span>商店概览</span>
            <el-button type="primary" size="small" @click="openAddSupplierDialog">商品配置</el-button>
            <el-button type="primary" size="small" @click="yypz">营业配置</el-button>
            <el-button type="success" size="small" @click="handleDetailQuery">查询</el-button>
          </div>
          <el-table :data="detailData" border>
            <el-table-column v-for="col in detailColumns" :key="col.prop" :prop="col.prop" :label="col.label" align="center" />
          </el-table>
        </div>
        <!-- 推广拉新 -->
        <div class="table-block">
          <div class="table-title">
            <span>推广拉新</span>
            <el-button type="success" size="small" @click="handlePromotionQuery">查询</el-button>
          </div>
          <el-table :data="promotionData" border style="margin-bottom: 24px;">
            <el-table-column v-for="col in promotionColumns" :key="col.prop" :prop="col.prop" :label="col.label" align="center" />
          </el-table>
        </div>
        <!-- 员工列表 -->
        <div class="table-block">
          <div class="table-title">
            <span>员工列表</span>
            <el-button type="primary" size="small" @click="openAddStaffDialog">新增员工</el-button>
            <el-button type="primary" size="small" @click="fhgl">封号管理</el-button>
            <el-button type="success" size="small" @click="handleStaffQuery">查询</el-button>
          </div>
          <el-table :data="staffData" border>
            <el-table-column v-for="col in staffColumns" :key="col.prop" :prop="col.prop" :label="col.label" align="center" />
            <el-table-column label="操作" align="center">
              <template #default="scope">
                <el-button type="danger" size="small" @click="() => handleDeleteStaff(scope.row)">删除</el-button>
              </template>
            </el-table-column>
          </el-table>
        </div>
        <!-- 供应列表 -->
        <div class="table-block">
          <div class="table-title">
            <span>供应列表</span>
            <el-button type="primary" size="small" @click="gystj">供应商添加</el-button>
            <el-button type="success" size="small" @click="handleSupplierQuery">查询</el-button>
          </div>
          <el-table
            :data="supplierData"
            border
            :scrollbar-always-on="true"
          >
            <el-table-column
              v-for="col in supplierColumns"
              :key="col.prop"
              :prop="col.prop"
              :label="col.label"
              align="center"
              :min-width="col.minWidth || '120px'"
            />
            <el-table-column
              label="操作"
              align="center"
              fixed="right"
              width="260"
            >
              <template #default="scope">
                <div class="supplier-action-btns">
                  <el-button
                    size="small"
                    type="primary"
                    plain
                    round
                    @click="() => handleSupplierAction(scope.row, '上线')"
                  >上线</el-button>
                  <el-button
                    size="small"
                    type="warning"
                    plain
                    round
                    @click="() => handleSupplierAction(scope.row, '下线')"
                  >下线</el-button>
                  <el-button
                    size="small"
                    type="info"
                    plain
                    round
                    @click="() => openQualificationDialog(scope.row, '资质')"
                  >资质</el-button>
                </div>
              </template>
            </el-table-column>
          </el-table>
        </div>
      </div>
    </el-card>
    <QualificationDialog
        v-model:visible="showQualificationDialog"
        :form="qualificationForm"
        @close="closeQualificationDialog"
        @submit="submitQualification"
    /> 
    <AddSupplierDialog
       v-model:visible="showAddSupplierDialog"
      @submit="handleAddSupplierSubmit"
    /> 
    <AddStaffDialog
      v-model:visible="showAddStaffDialog"
      @submit="handleAddStaffSubmit"
    />
    <BanDialog
      v-model:visible="showBanDialog"
      @submit="handleBanSubmit"
    />
    <SupplierAddDialog
      v-model:visible="showSupplierAddDialog"
      @submit="handleSupplierAddSubmit"
    />
    <BusinessConfigDialog
      v-model:visible="showBusinessConfigDialog"
      @submit="handleBusinessConfigSubmit"
    />
  </div>
</template>

<script setup>
import { ref, onMounted, reactive } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'
import { User, Goods, Money } from '@element-plus/icons-vue'
import QualificationDialog from './QualificationDialog.vue'
import AddSupplierDialog from './AddSupplierDialog.vue'
import AddStaffDialog from './AddStaffDialog.vue'
import BanDialog from './BanDialog.vue'
import SupplierAddDialog from './SupplierAddDialog.vue'
import BusinessConfigDialog from './BusinessConfigDialog.vue'

// 商店概览 mock 数据
const overviewData = ref([])
const dateRange = ref([])
const showQualificationDialog = ref(false)
const qualificationForm = reactive({
  name: '',
  phone: '',
  idFront: '',
  idBack: '',
  supplierName: '',
  supplierAddress: '',
  supplierType: '内供',
  license: '',
  foodPermit: '',
  healthPermit: ''
})
const openQualificationDialog = (row) => {
  showQualificationDialog.value = true
  qualificationForm.name = ''
  qualificationForm.phone = ''
  qualificationForm.idFront = ''
  qualificationForm.idBack = ''
  qualificationForm.supplierName = row.name || ''
  qualificationForm.supplierAddress = ''
  qualificationForm.supplierType = '内供'
  qualificationForm.license = ''
  qualificationForm.foodPermit = ''
  qualificationForm.healthPermit = ''
}
const closeQualificationDialog = () => {
  showQualificationDialog.value = false
}
const submitQualification = () => {
  ElMessage.success('操作成功')
  showQualificationDialog.value = false
}
// 商店概览表头
const overviewColumns = [
  { prop: 'register', label: '注册量', minWidth: '80px' },
  { prop: 'order', label: '下单量', minWidth: '80px' },
  { prop: 'pay', label: '支付量', minWidth: '80px' },
  { prop: 'refund', label: '退款量', minWidth: '80px' },
  { prop: 'sale', label: '销售金额', minWidth: '100px' },
  { prop: 'refundAmount', label: '退款金额', minWidth: '100px' },
  { prop: 'supplyCost', label: '供应成本', minWidth: '100px' },
  { prop: 'supplyRefund', label: '供应返点金额', minWidth: '120px' },
  { prop: 'supplySingle', label: '供应结算金额', minWidth: '140px' },
  { prop: 'profit', label: '毛利', minWidth: '80px' },
  { prop: 'repurchase', label: '复购率', minWidth: '80px' },
  { prop: 'invoice', label: '开票金额', minWidth: '100px' }
]

const showAddSupplierDialog = ref(false)
const showAddStaffDialog = ref(false)
const showBanDialog = ref(false)
const showSupplierAddDialog = ref(false)
const showBusinessConfigDialog = ref(false)
const openAddSupplierDialog = () => {
  showAddSupplierDialog.value = true
}
const openAddStaffDialog = () => {
  showAddStaffDialog.value = true
}
const fhgl = () => {
  showBanDialog.value = true
}
const handleAddSupplierSubmit = (formData) => {
  // 这里可以将formData添加到supplierData或发请求
  ElMessage.success('添加成功')
  showAddSupplierDialog.value = false
}
const handleAddStaffSubmit = (formData) => {
  // 这里可以将formData添加到staffData或发请求
  ElMessage.success('添加成功')
  showAddStaffDialog.value = false
}
const handleBanSubmit = (formData) => {
  // 这里可以处理封号数据
  ElMessage.success('添加成功')
  showBanDialog.value = false
}
const gystj = () => {
  showSupplierAddDialog.value = true
}
const handleSupplierAddSubmit = (formData) => {
  // 这里可以将formData添加到supplierData或发请求
  ElMessage.success('添加成功')
  showSupplierAddDialog.value = false
}
const yypz = () => {
  showBusinessConfigDialog.value = true
}
const handleBusinessConfigSubmit = (formData) => {
  // 这里可以处理营业配置数据
  ElMessage.success('变更成功')
  showBusinessConfigDialog.value = false
}
// 商店概览查询
const fetchOverview = () => {
  // 模拟接口
  setTimeout(() => {
    overviewData.value = [{
      register: 5000,
      order: 5000,
      pay: 3000,
      refund: 5000,
      sale: 3000,
      refundAmount: 3000,
      supplyCost: 5000,
      supplyRefund: 3000,
      supplySingle: 5000,
      profit: 3000,
      repurchase: '25%',
      invoice: 300
    }]
  }, 500)
}

// 商店概览 mock 数据
const detailData = ref([])
// 商店概览表头
const detailColumns = [
  { prop: 'status', label: '营业状态' },
  { prop: 'time', label: '打烊时间' },
  { prop: 'pack', label: '打包费' },
  { prop: 'delivery', label: '配送费' },
  { prop: 'start', label: '起送金额' },
  { prop: 'free', label: '0配送费标准' },
  { prop: 'promise', label: '履约时间标准' }
]
const fetchDetail = () => {
  setTimeout(() => {
    detailData.value = [
      {
        status: '营业中',
        time: '22:00',
        pack: '2元',
        delivery: '5元',
        start: '20元',
        free: '满50元',
        promise: '30分钟'
      }
    ]
  }, 500)
}

const handleOverviewQuery = () => {
  if (!dateRange.value || dateRange.value.length !== 2) {
    ElMessage.warning('请选择日期范围')
    return
  }
  fetchOverview()
  ElMessage.success('商店概览已刷新')
}
const handleDetailQuery = () => {
  fetchDetail()
  ElMessage.success('商店概览已刷新')
}

// 推广拉新 mock 数据和表头
const promotionData = ref([])
const promotionColumns = [
  { prop: 'promoter', label: '推广员' },
  { prop: 'account', label: '推广员账号' },
  { prop: 'current', label: '本月拉新量' },
  { prop: 'last', label: '上月拉新量' }
]
const fetchPromotion = () => {
  setTimeout(() => {
    promotionData.value = [
      { promoter: '张三', account: 'zhangsan01', current: 12, last: 8 },
      { promoter: '李四', account: 'lisi02', current: 20, last: 15 }
    ]
  }, 500)
}
const handlePromotionQuery = () => {
  fetchPromotion()
  ElMessage.success('推广拉新已刷新')
}

// 员工列表 mock 数据和表头
const staffData = ref([])
const staffColumns = [
  { prop: 'id', label: '员工id' },
  { prop: 'name', label: '员工姓名' },
  { prop: 'phone', label: '员工电话' },
  { prop: 'owner', label: '归属主体' },
  { prop: 'role', label: '角色' }
]
const fetchStaff = () => {
  setTimeout(() => {
    staffData.value = [
      { id: 1, name: '王五', phone: '13800000001', owner: '门店A', role: '店员' },
      { id: 2, name: '赵六', phone: '13800000002', owner: '门店B', role: '管理员' }
    ]
  }, 500)
}
const handleStaffQuery = () => {
  fetchStaff()
  ElMessage.success('员工列表已刷新')
}

const handleDeleteStaff = (row) => {
  ElMessageBox.confirm(
    `确定要删除员工「${row.name}」吗？`,
    '提示',
    {
      confirmButtonText: '确定',
      cancelButtonText: '取消',
      type: 'warning',
    }
  ).then(() => {
    staffData.value = staffData.value.filter(item => item.id !== row.id)
    ElMessage.success('删除成功')
  }).catch(() => {
    ElMessage.info('已取消删除')
  })
}

// 供应列表 mock 数据和表头
const supplierData = ref([])
const supplierColumns = [
  { prop: 'id', label: '供应商ID', minWidth: '120px' },
  { prop: 'name', label: '供应名称', minWidth: '120px' },
  { prop: 'type', label: '供应属性', minWidth: '120px' },
  { prop: 'permit', label: '供应许可', minWidth: '120px' },
  { prop: 'status', label: '供应状态', minWidth: '120px' },
  { prop: 'rate', label: '返点比例', minWidth: '120px' },
  { prop: 'category', label: '商品种类数量', minWidth: '120px' },
  { prop: 'sale', label: '售罄量', minWidth: '120px' },
  { prop: 'waitReceive', label: '待收单量', minWidth: '120px' },
  { prop: 'waitSend', label: '待配送量', minWidth: '120px' },
  { prop: 'todaySupply', label: '今日供应额', minWidth: '120px' },
  { prop: 'todayRefund', label: '今日退款额', minWidth: '120px' }
]
const fetchSupplier = () => {
  setTimeout(() => {
    supplierData.value = [
      {
        id: 'S001',
        name: '杭州供应商A',
        type: '直营',
        permit: '有',
        status: '正常',
        rate: '10%',
        category: 12,
        sale: 500,
        waitReceive: 10,
        waitSend: 5,
        todaySupply: 2000,
        todayRefund: 100
      },
      {
        id: 'S002',
        name: '上海供应商B',
        type: '加盟',
        permit: '有',
        status: '暂停',
        rate: '8%',
        category: 8,
        sale: 300,
        waitReceive: 6,
        waitSend: 2,
        todaySupply: 1200,
        todayRefund: 50
      }
    ]
  }, 500)
}
const handleSupplierQuery = () => {
  fetchSupplier()
  ElMessage.success('供应列表已刷新')
}

// 操作项方法
const handleSupplierAction = (row, action) => {
  ElMessage.success(`提示后续取接口  对供应商「${row.name}」执行了操作：${action}`)
}

// 页面加载时自动获取
onMounted(() => {
  fetchOverview()
  fetchDetail()
  fetchPromotion()
  fetchStaff()
  fetchSupplier()
})
</script>

<style scoped>
.home-container {
  padding: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.data-card {
  margin-bottom: 20px;
}

.data-card-header {
  display: flex;
  align-items: center;
  gap: 8px;
}

.data-card-content {
  text-align: center;
  padding: 20px 0;
}

.number {
  font-size: 24px;
  font-weight: bold;
  color: #409EFF;
}

.table-block {
  background: #fff;
  padding: 16px;
  margin-bottom: 24px;
  border-radius: 4px;
}
.table-title {
  display: flex;
  align-items: center;
  margin-bottom: 8px;
  gap: 16px;
}
.table-title1 {
  line-height: 48px;
}
.table-date {
  flex: 1;
  color: #666;
  font-size: 14px;
}

.supplier-action-btns {
  display: flex;
  gap: 10px;
  justify-content: center;
  align-items: center;
  padding-right: 16px;
}

.supplier-action-btns .el-button {
  min-width: 48px;
  font-size: 13px;
  border-radius: 16px;
  padding: 4px 12px;
  border-width: 1.2px;
  transition: all 0.2s;
}

.el-table__body-wrapper tr td:last-child,
.el-table__header-wrapper tr th:last-child {
  background: #f8fafc;
}

.table-block {
  background: #fff;
  padding: 16px;
  margin-bottom: 24px;
  border-radius: 4px;
}
.card-content {
  padding-left: 8px;
}
</style> 
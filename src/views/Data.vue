<template>
  <div class="data-container">
    <el-card class="data-card-ui">
      <el-form :inline="true" class="filter-form" label-width="120px">
        <el-form-item label="用户账号">
          <el-input v-model="filters.userAccount" placeholder="用户账号" clearable style="width: 160px" />
        </el-form-item>
        <el-form-item label="用户单号">
          <el-input v-model="filters.userOrderNo" placeholder="用户单号" clearable style="width: 160px" />
        </el-form-item>
        <el-form-item label="供应单号">
          <el-input v-model="filters.supplierOrderNo" placeholder="供应单号" clearable style="width: 160px" />
        </el-form-item>
        <el-form-item label="用户单状态">
          <el-select v-model="filters.userOrderStatus" placeholder="请选择" clearable style="width: 160px">
            <el-option label="全部" value="" />
            <el-option label="已完成" value="done" />
            <el-option label="未完成" value="pending" />
          </el-select>
        </el-form-item>
        <el-form-item label="送达形式">
          <el-select v-model="filters.deliveryType" placeholder="请选择" clearable style="width: 160px">
            <el-option label="自提" value="self" />
            <el-option label="配送" value="delivery" />
          </el-select>
        </el-form-item>
        <el-form-item label="退款状态">
          <el-select v-model="filters.refundStatus" placeholder="请选择" clearable style="width: 160px">
            <el-option label="全部" value="" />
            <el-option label="已退款" value="refunded" />
            <el-option label="未退款" value="not_refunded" />
          </el-select>
        </el-form-item>
        <el-form-item label="退款方">
          <el-select v-model="filters.refundParty" placeholder="请选择" clearable style="width: 160px">
            <el-option label="用户" value="user" />
            <el-option label="平台" value="platform" />
          </el-select>
        </el-form-item>
        <el-form-item label="发票申请状态">
          <el-select v-model="filters.invoiceStatus" placeholder="请选择" clearable style="width: 160px">
            <el-option label="全部" value="" />
            <el-option label="已申请" value="applied" />
            <el-option label="未申请" value="not_applied" />
          </el-select>
        </el-form-item>
        <el-form-item label="商品名称">
          <el-input v-model="filters.productName" placeholder="商品名称" clearable style="width: 160px" />
        </el-form-item>
        <el-form-item label="供应名称">
          <el-input v-model="filters.supplierName" placeholder="供应名称" clearable style="width: 160px" />
        </el-form-item>
        <el-form-item label="供应属性">
          <el-select v-model="filters.supplierType" placeholder="请选择" clearable style="width: 160px">
            <el-option label="全部" value="" />
            <el-option label="直营" value="direct" />
            <el-option label="加盟" value="franchise" />
          </el-select>
        </el-form-item>
        <el-form-item label="封号状态">
          <el-select v-model="filters.banStatus" placeholder="请选择" clearable style="width: 160px">
            <el-option label="全部" value="" />
            <el-option label="已封号" value="banned" />
            <el-option label="未封号" value="not_banned" />
          </el-select>
        </el-form-item>
        <el-form-item label="品类1">
          <el-select 
            v-model="filters.category1" 
            placeholder="请选择" 
            clearable 
            style="width: 160px"
            @change="handleCategory1Change"
          >
            <el-option
              v-for="item in categoryOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="品类2">
          <el-select 
            v-model="filters.category2" 
            placeholder="请选择" 
            clearable 
            style="width: 160px"
            @change="handleCategory2Change"
          >
            <el-option
              v-for="item in category2Options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="品类3">
          <el-select 
            v-model="filters.category3" 
            placeholder="请选择" 
            clearable 
            style="width: 160px"
          >
            <el-option
              v-for="item in category3Options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="下单日期">
          <el-date-picker
            v-model="filters.orderDate"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            value-format="YYYY-MM-DD"
            style="width: 260px"
          />
        </el-form-item>
        <el-form-item label="退款申请日期">
          <el-date-picker
            v-model="filters.refundDate"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            value-format="YYYY-MM-DD"
            style="width: 240px"
          />
        </el-form-item>
        <el-form-item label="履约总用时(分钟)">
          <el-input-number
            v-model="filters.promiseMin"
            :min="0"
            :max="999"
            style="width: 200px"
            placeholder="最小"
          />
          <span style="margin: 0 16px;">-</span>
          <el-input-number
            v-model="filters.promiseMax"
            :min="0"
            :max="999"
            style="width: 200px"
            placeholder="最大"
          />
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleSearch">查询</el-button>
          <el-button @click="handleReset">重置</el-button>
        </el-form-item>
      </el-form>
    </el-card>
    <el-tabs v-model="activeTab" class="data-tabs-ui" style="margin-top: 18px;">
      <el-tab-pane label="用户订单列表" name="userOrder">
        <div class="table-header">
          <span></span>
          <el-button type="success" size="small" @click="exportTable('userOrder')">导出</el-button>
        </div>
        <el-table :data="userOrderData" border>
          <el-table-column prop="userOrderNo" label="用户单号" />
          <el-table-column prop="orderStatus" label="订单状态" />
          <el-table-column prop="orderDate" label="下单日期" />
          <el-table-column prop="orderTime" label="支付时间" />
          <el-table-column prop="userAccount" label="用户账号" />
          <el-table-column prop="orderAmount" label="订单商品量" />
          <el-table-column prop="productAmount" label="订单总额"  />
          <el-table-column prop="productSellAmount" label="商品销售金额"  />
          <el-table-column prop="packingFee" label="打包费" />
          <el-table-column prop="deliveryFee" label="配送费" />
          <el-table-column prop="refundAmount" label="退款总额" />
          <el-table-column prop="plannedTime" label="履约计划用时(分钟)" />
          <el-table-column prop="actualTime" label="配送用时(分钟)" />
          <el-table-column prop="promiseTime" label="履约总用时(分钟)" />
          <el-table-column prop="invoiceStatus" label="发票申请状态" />
          <el-table-column label="操作" fixed="right">
            <template #default="scope">
              <el-button type="primary" size="small" link @click="handleAction('查看', scope.row)">查看</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-tab-pane>
      <el-tab-pane label="供应订单列表" name="supplierOrder">
        <div class="table-header">
          <span></span>
          <el-button type="success" size="small" @click="exportTable('supplierOrder')">导出</el-button>
        </div>
        <el-table :data="supplierOrderData" border>
          <el-table-column prop="supplierOrderNo" label="供应单号" />
          <el-table-column prop="supplierName" label="供应名称" />
          <el-table-column prop="deliveryType" label="供应属性" />
          <el-table-column prop="orderDate" label="下单日期" />
          <el-table-column prop="orderTime" label="下单时间" />
          <el-table-column prop="orderAmount" label="订单总额" />
          <el-table-column prop="supplierRefundAmount" label="退款总额" />
          <el-table-column prop="supplierAmount" label="供应返点金额" />
          <el-table-column prop="waitReceive" label="供应待结算金额" />
          <el-table-column prop="waitSend" label="供应收单用时（分钟）" />
          <el-table-column prop="waitDelivery" label="供应打包用时（分钟）" />
          <el-table-column label="操作" fixed="right">
            <template #default="scope">
              <el-button type="primary" size="small" link @click="handleAction('查看', scope.row)">查看</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-tab-pane>
      <el-tab-pane label="退款订单列表" name="refundOrder">
        <div class="table-header">
          <span></span>
          <el-button type="success" size="small" @click="exportTable('refundOrder')">导出</el-button>
        </div>
        <el-table :data="refundOrderData" border>
          <el-table-column prop="refundOrderNo" label="退款单号">
            <template #default="scope">
              {{ scope.row.refundOrderNo || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="userOrderNo" label="用户单号">
            <template #default="scope">
              {{ scope.row.userOrderNo || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="userAccount" label="用户账号">
            <template #default="scope">
              {{ scope.row.userAccount || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="peopleName" label="退款方">
            <template #default="scope">
              {{ scope.row.peopleName || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundReason" label="退款原因">
            <template #default="scope">
              {{ scope.row.refundReason || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundDate" label="申请日期">
            <template #default="scope">
              {{ scope.row.refundDate || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundTime" label="申请时间">
            <template #default="scope">
              {{ scope.row.refundTime || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundStatus" label="退款状态">
            <template #default="scope">
              {{ scope.row.refundStatus || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundFailReason" label="退款失败原因">
            <template #default="scope">
              {{ scope.row.refundFailReason || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundFinishDate" label="退款完成日期">
            <template #default="scope">
              {{ scope.row.refundFinishDate || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundFinishTime" label="退款完成时间">
            <template #default="scope">
              {{ scope.row.refundFinishTime || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundWechatOrderNo" label="微信退款订单号">
            <template #default="scope">
              {{ scope.row.refundWechatOrderNo || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundWechatSerialNo" label="微信退款流水号">
            <template #default="scope">
              {{ scope.row.refundWechatSerialNo || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundBankSerialNo" label="银行交易流水号">
            <template #default="scope">
              {{ scope.row.refundBankSerialNo || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundAmount" label="退款总额">
            <template #default="scope">
              {{ scope.row.refundAmount || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="productRefundAmount" label="商品退款金额">
            <template #default="scope">
              {{ scope.row.productRefundAmount || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundPackingFee" label="退款打包费">
            <template #default="scope">
              {{ scope.row.refundPackingFee || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="refundDeliveryFee" label="退款配送费">
            <template #default="scope">
              {{ scope.row.refundDeliveryFee || '--' }}
            </template>
          </el-table-column>
          <el-table-column label="操作" fixed="right">
            <template #default="scope">
              <el-button type="primary" size="small" link @click="handleAction('查看', scope.row)">查看</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-tab-pane>
      <el-tab-pane label="供应商品列表" name="supplierProduct">
        <div class="table-header">
          <span></span>
          <el-button type="success" size="small" @click="exportTable('supplierProduct')">导出</el-button>
        </div>
        <el-table :data="supplierProductData" border>
          <el-table-column prop="productNo" label="商品ID" />
          <el-table-column prop="productName" label="商品名称" />
          <el-table-column prop="addDate" label="添加日期" />
          <el-table-column prop="category1" label="品类1" />
          <el-table-column prop="category2" label="品类2" />
          <el-table-column prop="category3" label="品类3" />
          <el-table-column prop="supplierName" label="供应名称" />
          <el-table-column prop="deliveryType" label="供应属性" />
          <el-table-column prop="supplyPrice" label="供应价格(动态)" />
          <el-table-column prop="salePrice" label="销售价格(动态)" />
          <el-table-column prop="grossProfit" label="毛利率(商品)" />
          <el-table-column prop="productAmount" label="商品数量" />
          <el-table-column prop="productDailySales" label="商品日均销量" />
          <el-table-column prop="productRefundAmount" label="商品退款量" />
          <el-table-column prop="productSaleAmount" label="商品销售金额" />
          <el-table-column prop="productRefundAmount" label="商品退款金额" />
          <el-table-column width="180" label="操作" fixed="right">
            <template #default="scope">
              <div style="display: flex; gap: 10px; align-items: center;">
                <el-button type="danger" size="small" link @click="handleAction('删除', scope.row)">删除</el-button>
                <el-button type="primary" size="small" link @click="handleAction('上架', scope.row)">上架</el-button>
                <el-button type="warning" size="small" link @click="handleAction('下架', scope.row)">下架</el-button>
              </div>
            </template>
          </el-table-column>
        </el-table>
      </el-tab-pane>
      <el-tab-pane label="用户单号" name="userInfo">
        <div class="table-header">
          <span></span>
          <el-button type="success" size="small" @click="exportTable('userInfo')">导出</el-button>
        </div>
        <el-table :data="userInfoData" border>
          <el-table-column prop="userAccount" label="用户账号">
            <template #default="scope">
              {{ scope.row.userAccount || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="registerDate" label="注册日期">
            <template #default="scope">
              {{ scope.row.registerDate || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="orderCount" label="日均消费次数">
            <template #default="scope">
              {{ scope.row.orderCount || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="orderAmount" label="用户消费金额">
            <template #default="scope">
              {{ scope.row.orderAmount || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="userStatus" label="用户状态">
            <template #default="scope">
              {{ scope.row.userStatus || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="userRefundAmount" label="用户退款金额">
            <template #default="scope">
              {{ scope.row.userRefundAmount || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="banStartDate" label="封号开始日期">
            <template #default="scope">
              {{ scope.row.banStartDate || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="banStatus" label="封号时长">
            <template #default="scope">
              {{ scope.row.banStatus || '--' }}
            </template>
          </el-table-column>
          <el-table-column prop="banReason" label="封号原因">
            <template #default="scope">
              {{ scope.row.banReason || '--' }}
            </template>
          </el-table-column>
        </el-table>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'

const currentPage = ref(1)
const pageSize = ref(10)
const total = ref(100)

const tableData = ref([
  {
    id: 1,
    name: '测试数据1',
    date: '2024-03-20',
    status: '启用'
  },
  {
    id: 2,
    name: '测试数据2',
    date: '2024-03-21',
    status: '禁用'
  },
  {
    id: 3,
    name: '测试数据3',
    date: '2024-03-22',
    status: '启用'
  }
])


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

const handleSizeChange = (val) => {
  pageSize.value = val
  // 这里应该调用获取数据的API
}

const handleCurrentChange = (val) => {
  currentPage.value = val
  // 这里应该调用获取数据的API
}

const handleAdd = () => {
  ElMessage.success('点击了新增按钮')
}

const handleEdit = (row) => {
  ElMessage.success(`编辑数据：${row.name}`)
}

const handleDelete = (row) => {
  ElMessageBox.confirm(
    `确定要删除 ${row.name} 吗？`,
    '警告',
    {
      confirmButtonText: '确定',
      cancelButtonText: '取消',
      type: 'warning',
    }
  ).then(() => {
    ElMessage.success('删除成功')
  }).catch(() => {
    ElMessage.info('已取消删除')
  })
}

const filters = ref({
  userAccount: '',
  userOrderNo: '',
  supplierOrderNo: '',
  userOrderStatus: '',
  deliveryType: '',
  refundStatus: '',
  refundParty: '',
  invoiceStatus: '',
  supplierName: '',
  supplierType: '',
  category1: '',
  category2: '',
  category3: '',
  productName: '',
  banStatus: '',
  orderDate: [],
  refundDate: [],
  promiseMin: null,
  promiseMax: null
})

const handleSearch = () => {
  // 查询逻辑
  ElMessage.success('查询功能待实现')
}

const handleReset = () => {
  Object.keys(filters.value).forEach(key => {
    if (Array.isArray(filters.value[key])) {
      filters.value[key] = []
    } else {
      filters.value[key] = ''
    }
  })
  filters.value.promiseMin = null
  filters.value.promiseMax = null
}

const activeTab = ref('userOrder')

// 示例数据
const userOrderData = ref([])
const supplierOrderData = ref([])
const refundOrderData = ref([])
const supplierProductData = ref([])
const userInfoData = ref([])

const exportTable = (type) => {
  // 导出逻辑
  ElMessage.success(`导出${type}表格`)
}

userOrderData.value = [
  {
    userOrderNo: 'AGABA209823127', orderStatus: '待配送', orderDate: '2025-2-1', orderTime: '12:21', userAccount: '18799002233', orderAmount: 3000, productAmount: 5000, productSellAmount:2, packingFee: 3, deliveryFee: 1, refundAmount: 100, plannedTime: 30, actualTime: 28, promiseTime: 28, invoiceStatus: '发票发送成功', action: '查看'
  },
  {
    userOrderNo: 'BGABA209823128', orderStatus: '已完成', productSellAmount: 1, orderDate: '2025-2-2', orderTime: '13:21', userAccount: '18799002234', orderAmount: 4000, productAmount: 6000, packingFee: 4, deliveryFee: 2, refundAmount: 200, plannedTime: 40, actualTime: 38, promiseTime: 38, invoiceStatus: '发票发送成功', action: '查看'
  }
]
supplierOrderData.value = [
  {
    supplierOrderNo: 'SO202403200001',
    supplierName: '星巴克咖啡',
    deliveryType: '直营',
    orderDate: '2024-03-20',
    orderTime: '14:30:25',
    orderAmount: '1288.00',
    supplierRefundAmount: '88.00',
    supplierAmount: '1200.00',
    waitReceive: '1000.00',
    waitSend: 15,
    waitDelivery: 25
  },
  {
    supplierOrderNo: 'SO202403200002',
    supplierName: '瑞幸咖啡',
    deliveryType: '加盟',
    orderDate: '2024-03-20',
    orderTime: '15:45:10',
    orderAmount: '2566.00',
    supplierRefundAmount: '166.00',
    supplierAmount: '2400.00',
    waitReceive: '2000.00',
    waitSend: 20,
    waitDelivery: 30
  }
]
refundOrderData.value = [
  {
    refundOrderNo: 'RF202403200001',
    userOrderNo: 'SO202403200001',
    userAccount: '13812345678',
    peopleName: '张三',
    refundReason: '商品质量问题',
    refundDate: '2024-03-20',
    refundTime: '15:30:25',
    refundStatus: '退款成功',
    refundFailReason: '',
    refundFinishDate: '2024-03-20',
    refundFinishTime: '15:35:10',
    refundWechatOrderNo: '4200001234202403201234567890',
    refundWechatSerialNo: '4200001234202403201234567890',
    refundBankSerialNo: '202403201234567890',
    refundAmount: '128.00',
    productRefundAmount: '120.00',
    refundPackingFee: '3.00',
    refundDeliveryFee: '5.00'
  },
  {
    refundOrderNo: 'RF202403200002',
    userOrderNo: 'SO202403200002',
    userAccount: '13987654321',
    peopleName: '李四',
    refundReason: '配送超时',
    refundDate: '2024-03-20',
    refundTime: '16:45:30',
    refundStatus: '退款失败',
    refundFailReason: '银行系统繁忙，请稍后重试',
    refundFinishDate: '',
    refundFinishTime: '',
    refundWechatOrderNo: '4200001234202403202345678901',
    refundWechatSerialNo: '4200001234202403202345678901',
    refundBankSerialNo: '202403202345678901',
    refundAmount: '166.00',
    productRefundAmount: '158.00',
    refundPackingFee: '4.00',
    refundDeliveryFee: '4.00'
  }
]
supplierProductData.value = [
  {
    productNo: 'SP202403200001',
    productName: '美式咖啡',
    addDate: '2024-03-20',
    category1: '饮品',
    category2: '咖啡',
    category3: '经典',
    supplierName: '星巴克咖啡',
    deliveryType: '直营',
    supplyPrice: '15.00',
    salePrice: '22.00',
    grossProfit: '31.82%',
    productAmount: 1000,
    productDailySales: 85,
    productRefundAmount: 5,
    productSaleAmount: '1870.00',
    productRefundAmount: '110.00'
  },
  {
    productNo: 'SP202403200002',
    productName: '抹茶拿铁',
    addDate: '2024-03-20',
    category1: '饮品',
    category2: '奶茶',
    category3: '新品',
    supplierName: '瑞幸咖啡',
    deliveryType: '加盟',
    supplyPrice: '18.00',
    salePrice: '26.00',
    grossProfit: '30.77%',
    productAmount: 800,
    productDailySales: 65,
    productRefundAmount: 3,
    productSaleAmount: '1690.00',
    productRefundAmount: '78.00'
  }
]
userInfoData.value = [
  {
    userAccount: '13812345678',
    registerDate: '2024-01-15',
    orderCount: 3.5,
    orderAmount: '2566.00',
    userStatus: '正常',
    userRefundAmount: '166.00',
    banStartDate: '',
    banStatus: '',
    banReason: ''
  },
  {
    userAccount: '13987654321',
    registerDate: '2024-02-20',
    orderCount: 2.8,
    orderAmount: '1888.00',
    userStatus: '已封号',
    userRefundAmount: '288.00',
    banStartDate: '2024-03-15',
    banStatus: '30天',
    banReason: '恶意退款'
  }
]

const handleAction = (action, row) => {
  if (action === '删除') {
    ElMessageBox.confirm(
      `确定要删除商品"${row.productName}"吗？`,
      '删除确认',
      {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning',
      }
    ).then(() => {
      // 这里添加删除逻辑
      ElMessage.success('删除成功')
    }).catch(() => {
      ElMessage.info('已取消删除')
    })
  } else {
    ElMessage.info(`点击了${action}`)
  }
}

const category2Options = ref([])
const category3Options = ref([])

const handleCategory1Change = (value) => {
  filters.value.category2 = ''
  filters.value.category3 = ''
  category2Options.value = value ? cat2Map[value] || [] : []
  category3Options.value = []
}

const handleCategory2Change = (value) => {
  filters.value.category3 = ''
  category3Options.value = value ? cat3Map[value] || [] : []
}
</script>

<style scoped>
.data-container {
  padding: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.pagination-container {
  margin-top: 20px;
  display: flex;
  justify-content: flex-end;
}

.filter-form {
  background: #f5f7fa;
  padding: 18px 18px 0 18px;
  border-radius: 6px;
  margin-bottom: 18px;
  box-shadow: 0 1px 4px #0000000a;
}

.filter-form .el-form-item {
  margin-bottom: 12px;
  margin-right: 16px;
}

.data-card-ui {
  padding: 24px;
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 2px 12px #00000014;
}

.data-tabs-ui {
  background: #fff;
  border-radius: 8px;
  margin-bottom: 18px;
  box-shadow: 0 1px 8px #0000000a;
  padding: 24px 12px 12px 12px;
}

.table-header {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  margin-bottom: 8px;
  padding-right: 8px;
}

/* 表格美化 */
.el-table {
  border-radius: 6px;
  font-size: 14px;
  background: #fafbfc;
}

.el-table th, .el-table td {
  padding: 10px 8px;
}

.el-table th {
  background: #f5f7fa;
  color: #333;
  font-weight: 500;
}

.el-table__body tr:hover > td {
  background: #f0faff !important;
}
</style> 
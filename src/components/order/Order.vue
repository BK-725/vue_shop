<template>
    <div>
        <!-- 面包屑导航 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>订单管理</el-breadcrumb-item>
            <el-breadcrumb-item>订单列表</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- 卡片视图区域 -->
        <el-card class="box-card">
          <el-row>
            <el-col :span="8">
              <el-input placeholder="请输入内容">
              <el-button slot="append" icon="el-icon-search"></el-button>
              </el-input>
            </el-col>
          </el-row>
          <!-- 订单列表数据 -->
          <el-table :data="orderlist" border stripe>
            <el-table-column type="index"></el-table-column>
            <el-table-column label="订单编号" prop="order_number"></el-table-column>
            <el-table-column label="订单价格" prop="order_price"></el-table-column>
            <el-table-column label="是否付款" prop="pay_status">
              <template slot-scope="scope">
                <el-tag type="success" v-if="scope.row.pay_status === '1'">已付款</el-tag>
                <el-tag type="danger" v-else>未付款</el-tag>
              </template>
            </el-table-column>
            <el-table-column label="是否发货" prop="is_send"></el-table-column>
            <el-table-column label="下单时间" prop="create_time">
              <template slot-scope="scope">
                {{scope.row.create_time | dateFormat}}
              </template>
            </el-table-column>
            <el-table-column label="操作">
              <template>
                <el-button type="primary" icon="el-icon-edit" @click="showBox"></el-button>
                <el-button type="success" icon="el-icon-location-outline" @click="showProgressBox"></el-button>
              </template>
            </el-table-column>
          </el-table>
          <!-- 分页区域 -->
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="queryInfo.pagenum"
            :page-sizes="[5, 10, 15]"
            :page-size="queryInfo.pagesize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="total">
          </el-pagination>
        </el-card>
        <!-- 修改按钮对话框 -->
        <el-dialog
          title="提示"
          :visible.sync="addressVisible"
          width="50%"
          @close="addressDialogClosed">
        <el-form :model="addressForm" :rules="addressFormRules" ref="addressFormRef" label-width="100px">
          <el-form-item label="省市区/县" prop="address1">
              <el-cascader
                v-model="addressForm.address1"
                :options="cityData"
                :props="{ expandTrigger: 'hover' }">
              </el-cascader>
          </el-form-item>
          <el-form-item label="详细地址" prop="address2">
            <el-input v-model="addressForm.address2"></el-input>
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="addressVisible = false">取 消</el-button>
          <el-button type="primary" @click="addressVisible = false">确 定</el-button>
        </span>
        </el-dialog>
        <!-- 展示物流进度对话框 -->
        <el-dialog
          title="物流进度"
          :visible.sync="progressVisible"
          width="50%">
        <span>物流信息地址</span>
        </el-dialog>
    </div>
</template>

<script>
import cityData from './citydata.js'
export default {
  data () {
    return {
      // 服务器请求的数据
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 10
      },
      // total 代表总数据条数 默认为0
      total: 0,
      // 代表订单列表  默认为空数组
      orderlist: [],
      // 控制对话框的显示与隐藏
      addressVisible: false,
      addressForm: {
        address1: [],
        address2: ''
      },
      addressFormRules: {
        address1: [
          { required: true, message: '请选择省市区县' }
        ],
        address2: [
          { required: true, message: '请填写详细地址', triggle: 'blur' }
        ]
      },
      // 级联选择器的 城市
      cityData,
      // 控制物流管理的显示与隐藏
      progressVisible: false,
      progressInfo: []
    }
  },
  created () {
    this.getOrderList()
  },
  methods: {
    async getOrderList () {
      const { data: res } = await this.$http.get('orders', { params: this.queryInfo })
      if (res.meta.status !== 200) {
        return this.$message.error('获取订单列表失败')
      }
      console.log(res)
      this.total = res.data.total
      this.orderlist = res.data.goods
    },
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getOrderList()
    },
    handleCurrentChange (newPage) {
      this.queryInfo.pagenum = newPage
      this.getOrderList()
    },
    // 修改按钮
    showBox () {
      this.addressVisible = true
    },
    // 监听 对话框的关闭事件
    addressDialogClosed () {
      this.$refs.addressFormRef.resetFields()
    },
    // 展示物流进度的按钮
    // async showProgressBox () {
    // const { data: res } = await this.$http.get('/kuaidi/804909574412544580')
    // if (res.meta.status !== 200) {
    //   return this.$message.error('获取物流进度失败')
    // }
    // this.progressInfo = res.data
    // this.progressVisible = true
    // console.log(this.progressInfo)
    // }
    showProgressBox () {
      this.progressVisible = true
    }
  }
}
</script>

<style lang="less" scoped>
.el-cascader {
    width: 100%;
}
</style>

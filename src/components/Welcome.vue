<template>
    <div>
      <!-- 面包屑导航 -->
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>欢迎您！</el-breadcrumb-item>
      </el-breadcrumb>
      <!-- 卡片视图 -->
      <el-card class="box-card">
        <!-- 为ECharts准备一个具备大小（宽高）的Dom -->
        <div id="main" style="width: 900px; height:600px;"></div>
      </el-card>
    </div>
</template>

<script>
// 1.导入echarts
import * as echarts from 'echarts'
export default {
  data () {
    return {
      option: {
        legend: {},
        tooltip: {
          trigger: 'axis',
          showContent: false
        },
        dataset: {
          source: [
            ['product', '2016', '2017', '2018', '2019', '2020', '2021'],
            ['Matcha Latte', 41.1, 30.4, 65.1, 53.3, 83.8, 98.7],
            ['Milk Tea', 86.5, 92.1, 85.7, 83.1, 73.4, 55.1],
            ['Cheese Cocoa', 24.1, 67.2, 79.5, 86.4, 65.2, 82.5],
            ['Walnut Brownie', 55.2, 67.1, 69.2, 72.4, 53.9, 39.1]
          ]
        },
        xAxis: { type: 'category' },
        yAxis: { gridIndex: 0 },
        grid: { top: '55%' },
        series: [
          { type: 'line', smooth: true, seriesLayoutBy: 'row' },
          { type: 'line', smooth: true, seriesLayoutBy: 'row' },
          { type: 'line', smooth: true, seriesLayoutBy: 'row' },
          { type: 'line', smooth: true, seriesLayoutBy: 'row' },
          {
            type: 'pie',
            id: 'pie',
            radius: '30%',
            center: ['50%', '25%'],
            label: { formatter: '{b}: {@2012} ({d}%)' },
            encode: {
              itemName: 'product',
              value: '2017',
              tooltip: '2017'
            }
          }
        ]
      }
    }
  },
  created () {},
  // dom创建之后才会执行。此时页面上的元素已经被渲染完毕了！
  mounted () {
    // 3.基于准备好的dom，初始化echarts实例
    var myChart = echarts.init(document.getElementById('main'))
    myChart.on('updateAxisPointer', function (event) {
      var xAxisInfo = event.axesInfo[0]
      if (xAxisInfo) {
        var dimension = xAxisInfo.value + 1
        myChart.setOption({
          series: {
            id: 'pie',
            label: { formatter: '{b}: {@[' + dimension + ']} ({d}%)' },
            encode: { value: dimension, tooltip: dimension }
          }
        })
      }
    })
    // 4.准备数据和配置项
    myChart.setOption(this.option)
  }
}
</script>

<style lang="less" scoped>

</style>>

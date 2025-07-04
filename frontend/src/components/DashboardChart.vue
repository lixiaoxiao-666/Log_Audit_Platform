<template>
  <div class="dashboard-chart">
    <BaseChart
      :option="chartOption"
      :height="height"
      :width="width"
      :loading="loading"
      @click="handleChartClick"
    />
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import BaseChart from './BaseChart.vue'

interface Props {
  type: 'line' | 'bar' | 'pie' | 'gauge' | 'area' | 'scatter' | 'radar'
  data: any[]
  title?: string
  height?: string
  width?: string
  loading?: boolean
  colors?: string[]
  showLegend?: boolean
  showGrid?: boolean
  smooth?: boolean
  legendPosition?: 'top' | 'bottom' | 'left' | 'right' | 'top-left' | 'top-right' | 'bottom-left' | 'bottom-right'
}

const props = withDefaults(defineProps<Props>(), {
  height: '400px',
  width: '100%',
  loading: false,
  colors: () => ['#1890ff', '#52c41a', '#faad14', '#f5222d', '#722ed1', '#13c2c2'],
  showLegend: true,
  showGrid: true,
  smooth: false,
  legendPosition: 'bottom'
})

const emit = defineEmits<{
  chartClick: [params: any]
}>()

// 通用样式配置
const commonStyle = {
  textStyle: {
    fontFamily: '-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif'
  },
  color: props.colors
}

// 获取图例配置
const getLegendConfig = () => {
  const baseConfig = {
    show: props.showLegend,
    textStyle: {
      color: '#595959'
    },
    itemGap: 20, // 增加图例项之间的间距
    padding: [20, 0] // 增加图例的内边距，与图表保持距离
  }

  switch (props.legendPosition) {
    case 'top':
      return { ...baseConfig, top: 20 }
    case 'bottom':
      return { ...baseConfig, bottom: 20 }
    case 'left':
      return { ...baseConfig, left: 20, orient: 'vertical' }
    case 'right':
      return { ...baseConfig, right: 20, orient: 'vertical' }
    case 'top-left':
      return { ...baseConfig, top: 30, left: 30, orient: 'vertical', itemGap: 15 }
    case 'top-right':
      return { ...baseConfig, top: 20, right: 20, orient: 'vertical' }
    case 'bottom-left':
      return { ...baseConfig, bottom: 20, left: 20, orient: 'vertical' }
    case 'bottom-right':
      return { ...baseConfig, bottom: 20, right: 20, orient: 'vertical' }
    default:
      return { ...baseConfig, bottom: 20 }
  }
}

// 线图配置
const getLineOption = () => ({
  ...commonStyle,
  title: {
    text: props.title,
    left: 'center',
    textStyle: {
      fontSize: 16,
      fontWeight: 'bold',
      color: '#262626'
    }
  },
  tooltip: {
    trigger: 'axis',
    backgroundColor: 'rgba(255, 255, 255, 0.95)',
    borderColor: '#e8e8e8',
    borderWidth: 1,
    textStyle: {
      color: '#262626'
    },
    axisPointer: {
      type: 'cross',
      crossStyle: {
        color: '#999'
      }
    }
  },
  legend: getLegendConfig(),
  grid: {
    show: props.showGrid,
    left: '3%',
    right: '4%',
    bottom: props.showLegend ? '20%' : '3%',
    containLabel: true,
    borderColor: '#f0f0f0'
  },
  xAxis: {
    type: 'category',
    data: props.data.map(item => item.name || item.x),
    axisLine: {
      lineStyle: {
        color: '#d9d9d9'
      }
    },
    axisLabel: {
      color: '#8c8c8c'
    }
  },
  yAxis: {
    type: 'value',
    axisLine: {
      lineStyle: {
        color: '#d9d9d9'
      }
    },
    axisLabel: {
      color: '#8c8c8c'
    },
    splitLine: {
      lineStyle: {
        color: '#f0f0f0'
      }
    }
  },
  series: [{
    type: 'line',
    data: props.data.map(item => item.value || item.y),
    smooth: props.smooth,
    symbol: 'circle',
    symbolSize: 6,
    lineStyle: {
      width: 3
    },
    areaStyle: props.type === 'area' ? {
      opacity: 0.3
    } : undefined,
    emphasis: {
      focus: 'series',
      blurScope: 'coordinateSystem'
    }
  }]
})

// 柱状图配置
const getBarOption = () => ({
  ...commonStyle,
  title: {
    text: props.title,
    left: 'center',
    textStyle: {
      fontSize: 16,
      fontWeight: 'bold',
      color: '#262626'
    }
  },
  tooltip: {
    trigger: 'axis',
    backgroundColor: 'rgba(255, 255, 255, 0.95)',
    borderColor: '#e8e8e8',
    borderWidth: 1,
    textStyle: {
      color: '#262626'
    }
  },
  grid: {
    left: '3%',
    right: '4%',
    bottom: '3%',
    containLabel: true
  },
  xAxis: {
    type: 'category',
    data: props.data.map(item => item.name || item.x),
    axisLine: {
      lineStyle: {
        color: '#d9d9d9'
      }
    },
    axisLabel: {
      color: '#8c8c8c'
    }
  },
  yAxis: {
    type: 'value',
    axisLine: {
      lineStyle: {
        color: '#d9d9d9'
      }
    },
    axisLabel: {
      color: '#8c8c8c'
    },
    splitLine: {
      lineStyle: {
        color: '#f0f0f0'
      }
    }
  },
  series: [{
    type: 'bar',
    data: props.data.map(item => item.value || item.y),
    barWidth: '60%',
    itemStyle: {
      borderRadius: [4, 4, 0, 0]
    },
    emphasis: {
      focus: 'series'
    }
  }]
})

// 饼图配置
const getPieOption = () => ({
  ...commonStyle,
  title: {
    text: props.title,
    left: 'center',
    textStyle: {
      fontSize: 16,
      fontWeight: 'bold',
      color: '#262626'
    }
  },
  tooltip: {
    trigger: 'item',
    backgroundColor: 'rgba(255, 255, 255, 0.95)',
    borderColor: '#e8e8e8',
    borderWidth: 1,
    textStyle: {
      color: '#262626'
    },
    formatter: '{a} <br/>{b}: {c} ({d}%)'
  },
  legend: getLegendConfig(),
  series: [{
    name: props.title || '数据分布',
    type: 'pie',
    radius: ['30%', '50%'],
    center: props.legendPosition === 'top-left' ? ['60%', '55%'] : 
            props.legendPosition === 'bottom' ? ['50%', '45%'] : ['50%', '50%'],
    data: props.data.map(item => ({
      name: item.name,
      value: item.value
    })),
    emphasis: {
      itemStyle: {
        shadowBlur: 10,
        shadowOffsetX: 0,
        shadowColor: 'rgba(0, 0, 0, 0.5)'
      }
    },
    label: {
      show: true,
      formatter: '{b}: {d}%',
      position: 'outside',
      fontSize: 11
    },
    labelLine: {
      show: true,
      length: 10,
      length2: 8
    }
  }]
})

// 仪表盘配置
const getGaugeOption = () => ({
  ...commonStyle,
  title: {
    text: props.title,
    left: 'center',
    textStyle: {
      fontSize: 16,
      fontWeight: 'bold',
      color: '#262626'
    }
  },
  series: [{
    type: 'gauge',
    center: ['50%', '60%'],
    startAngle: 200,
    endAngle: -40,
    min: 0,
    max: 100,
    splitNumber: 10,
    itemStyle: {
      color: '#1890ff'
    },
    progress: {
      show: true,
      width: 30
    },
    pointer: {
      show: false
    },
    axisLine: {
      lineStyle: {
        width: 30
      }
    },
    axisTick: {
      distance: -45,
      splitNumber: 5,
      lineStyle: {
        width: 2,
        color: '#999'
      }
    },
    splitLine: {
      distance: -52,
      length: 14,
      lineStyle: {
        width: 3,
        color: '#999'
      }
    },
    axisLabel: {
      distance: -20,
      color: '#999',
      fontSize: 20
    },
    anchor: {
      show: false
    },
    title: {
      show: false
    },
    detail: {
      valueAnimation: true,
      width: '60%',
      lineHeight: 40,
      borderRadius: 8,
      offsetCenter: [0, '-15%'],
      fontSize: 60,
      fontWeight: 'bolder',
      formatter: '{value}%',
      color: 'inherit'
    },
    data: [{
      value: props.data[0]?.value || 0
    }]
  }]
})

// 雷达图配置
const getRadarOption = () => ({
  ...commonStyle,
  title: {
    text: props.title,
    left: 'center',
    textStyle: {
      fontSize: 16,
      fontWeight: 'bold',
      color: '#262626'
    }
  },
  tooltip: {
    trigger: 'item',
    backgroundColor: 'rgba(255, 255, 255, 0.95)',
    borderColor: '#e8e8e8',
    borderWidth: 1,
    textStyle: {
      color: '#262626'
    }
  },
  legend: getLegendConfig(),
  radar: {
    indicator: props.data.map(item => ({
      name: item.name,
      max: item.max || 100
    })),
    center: ['50%', '50%'],
    radius: '70%'
  },
  series: [{
    type: 'radar',
    data: [{
      value: props.data.map(item => item.value),
      name: props.title || '数据'
    }],
    areaStyle: {
      opacity: 0.3
    }
  }]
})

// 根据类型获取配置
const chartOption = computed(() => {
  switch (props.type) {
    case 'line':
    case 'area':
      return getLineOption()
    case 'bar':
      return getBarOption()
    case 'pie':
      return getPieOption()
    case 'gauge':
      return getGaugeOption()
    case 'radar':
      return getRadarOption()
    default:
      return getLineOption()
  }
})

const handleChartClick = (params: any) => {
  emit('chartClick', params)
}
</script>

<style scoped>
.dashboard-chart {
  width: 100%;
  height: 100%;
}
</style> 
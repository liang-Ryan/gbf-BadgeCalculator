<script setup>
// 通用
import { computed, onMounted, ref, watch } from 'vue'

// 组件
import SelectBar from './SelectBar.vue'

// Map
import { nameMap } from '../assets/nameMap.js'

// =============================
// 排位数据
// =============================

// 勋章要求
const finalsRankings = {
  crewRankings: [
    '30001位及以下',
    '18001~30000位',
    '14001~18000位',
    '9001~14000位',
    '5501~9000位',
    '2001~5500位',
    '2000位及以内'
  ],
  individualRankings: [
    '270001~370000位',
    '180001~270000位',
    '140001~180000位',
    '90001~140000位',
    '90000位及以内'
  ]
}

// =============================
// 选中数据
// =============================

const resultSelected = ref({
  crewRankings: '30001位及以下',
  individualRankings: '270001~370000位'
})

const setSelected = ([day, label], newValue) => {
  resultSelected.value[label] = newValue
}

// =============================
// 计算
// =============================

// 统计数量
const resultSum = ref(0)

// 计算器
const resultCounter = (selected) => {
  // computed
  const crewRankings = computed(() => {
    return selected.crewRankings
  })
  const individualRankings = computed(() => {
    return selected.individualRankings
  })

  // function
  let count = 0

  // 骑空团排位
  switch (crewRankings.value) {
    case '2000位及以内':
      count += 70
      break
    case '2001~5500位':
      count += 60
      break
    case '5501~9000位':
      count += 60
      break
    case '9001~14000位':
      count += 35
      break
    case '14001~18000位':
      count += 30
      break
    case '18001~30000位':
      count += 15
      break
    case '30001位及以下':
      count += 10
      break
  }

  // 个人排位
  switch (individualRankings.value) {
    case '90000位及以内':
      count += 100
      break
    case '90001~140000位':
      count += 75
      break
    case '140001~180000位':
      count += 50
      break
    case '180001~270000位':
      count += 25
      break
    case '270001~370000位':
      count += 10
      break
  }

  return count
}

// 监听
watch(
  resultSelected.value,
  () => {
    resultSum.value = resultCounter(resultSelected.value)
  },
  { deep: true }
)

onMounted(() => {
  resultSum.value = resultCounter(resultSelected.value)
})

// =============================
// defineExpose
// =============================

defineExpose({
  resultSum
})

// =============================
</script>

<template>
  <!-- 表头 -->
  <tr>
    <th colspan="2">本战排位</th>
    <th>获得勋章数</th>
  </tr>

  <!-- 表格 -->
  <tr v-for="(options, label, index) in finalsRankings" :key="label">
    <!-- 左侧表头 -->
    <th>{{ nameMap[label] }}</th>

    <!-- 选项 td在组件里 -->
    <SelectBar :options="options" :selected="[0, label]" @clickBar="setSelected"></SelectBar>

    <!-- 勋章计算 仅渲染一次 -->
    <td rowspan="2" style="text-align: center" v-if="index === 0">
      {{ resultSum }}
    </td>
  </tr>
</template>

<style scoped></style>

<script setup>
import { computed, ref, watch } from 'vue'

// 组件
import SelectBar from './SelectBar.vue'

// Map
import { nameMap } from '../assets/nameMap.js'

// =============================
// 预选数据
// =============================

// 勋章要求
const prelimsLine = {
  crewHoners: [0, 22000, 45000],
  individualHoners: [0, 1200, 2500, 12000, 25000],
  crewRankings: [
    '未加入骑空团',
    '36001位及以外',
    '19001~36000位',
    '8001~19000位',
    '1001~8000位',
    '301~1000位',
    '300位及以内'
  ]
}

// =============================
// 选中数据
// =============================

const prelimsSelected = ref({
  crewHoners: 0,
  individualHoners: 0,
  crewRankings: '未加入骑空团'
})

const setSelected = ([day, label], newValue) => {
  prelimsSelected.value[label] = newValue
}

// =============================
// 计算
// =============================

// 统计数量
const prelimsSum = ref(0)

// 计算器
const prelimsCounter = (selected) => {
  // computed
  const crewHoners = computed(() => {
    return selected.crewHoners
  })
  const individualHoners = computed(() => {
    return selected.individualHoners
  })
  const crewRankings = computed(() => {
    return selected?.crewRankings
  })

  // function
  let count = 0

  // 贡献度
  if (crewHoners.value >= 22000 && individualHoners.value >= 1200) {
    count += 5
  }
  if (crewHoners.value >= 45000 && individualHoners.value >= 2500) {
    count += 10
  }
  if (individualHoners.value >= 12000) {
    count += 5
  }
  if (individualHoners.value >= 25000) {
    count += 10
  }

  // 排位
  switch (crewRankings.value) {
    case '300位及以内':
      count += 45
      break
    case '301~1000位':
      count += 25
      break
    case '1001~8000位':
      count += 25
      break
    case '8001~19000位':
      count += 10
      break
    case '19001~36000位':
      count += 5
      break
  }

  return count
}

// 监听
watch(
  prelimsSelected.value,
  () => {
    prelimsSum.value = prelimsCounter(prelimsSelected.value)
    emitEvent()
  },
  { deep: true }
)

// =============================
// 传参
// =============================

const emit = defineEmits(['changeSum'])

const emitEvent = () => {
  emit('changeSum')
}

// =============================
// defineExpose
// =============================

defineExpose({
  prelimsSum
})

// =============================
</script>

<template>
  <!-- 表头 -->
  <tr>
    <th colspan="2">{{ nameMap['prelims'] }}</th>
    <th>获取勋章数</th>
  </tr>

  <!-- 表格 -->
  <tr v-for="(options, label) in prelimsLine" :key="label">
    <!-- 左侧表头 -->
    <th>{{ nameMap[label] }}</th>

    <!-- 选项 td在组件里 -->
    <SelectBar :options="options" :selected="[0, label]" @clickBar="setSelected"></SelectBar>

    <!-- 勋章计算 仅渲染一次 -->
    <td rowspan="3" style="text-align: center" v-if="label === 'crewHoners'">
      {{ prelimsSum }}
    </td>
  </tr>
</template>

<style scoped></style>

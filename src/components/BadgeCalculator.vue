<script setup>
// 通用
import { onMounted, ref, watch } from 'vue'

const props = defineProps({
  selected: Object,
  counter: Function
})

// =============================
// 勋章数量
// =============================

const sum = ref(0)

// =============================
// 监听 / 计算
// =============================

watch(
  props.selected,
  () => {
    sum.value = props.counter(props.selected)
  },
  { deep: true }
)

onMounted(() => {
  sum.value = props.counter(props.selected)
})

// =============================
</script>

<template>
  <div style="text-align: center">{{ sum }}</div>
</template>

<style scoped></style>

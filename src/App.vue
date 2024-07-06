<script setup>
// 通用
import { onMounted, ref, watch } from 'vue'

// 组件
import prelimsPart from './components/prelimsPart.vue'
const prelims = ref(null)
import finalsPart from './components/finalsPart.vue'
const finals = ref(null)
import resultPart from './components/resultPart.vue'
const result = ref(null)

// =============================
// 计算
// =============================

// 统计数量
const totalSum = ref(0)

onMounted(() => {
  watch(
    () => [
      prelims.value.prelimsSum,
      finals.value.finalsSum,
      result.value.resultSum
    ],
    () => {
      totalSum.value =
        prelims.value.prelimsSum +
        finals.value.finalsSum[1] +
        finals.value.finalsSum[2] +
        finals.value.finalsSum[3] +
        finals.value.finalsSum[4] +
        result.value.resultSum
    },
    { immediate: true, deep: true }
  )
})

// =============================
</script>

<template>
  <table class="wikitable BadgeCalculator" style="height: 100%; font-size: 12px;">
    <!-- 预选 -->
    <prelimsPart ref="prelims"></prelimsPart>

    <!-- 本战 -->
    <finalsPart ref="finals"></finalsPart>

    <!-- 特殊战斗 -->
    <resultPart ref="result"></resultPart>

    <!-- 总数 -->
    <tr>
      <th colspan="2">勋章 获取途径</th>
      <th>获得总数</th>
    </tr>
    <tr>
      <td colspan="2" style="text-align: center">
        <a href="https://gbf.huijiwiki.com/wiki/%E5%8F%A4%E6%88%98%E5%9C%BA">
          决战！星之古战场</a>
        和
        <a href="https://gbf.huijiwiki.com/wiki/%E5%85%AC%E4%BC%9A%E6%88%98">
          公会战</a>
      </td>
      <td style="text-align: center">
        {{ totalSum }}
      </td>
    </tr>
  </table>
</template>

<style></style>
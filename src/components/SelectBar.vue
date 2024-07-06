<script setup>
// 通用
import { ref } from 'vue'

// Map
import { valueMap } from '../assets/nameMap.js'

// 接收参数
const props = defineProps({
  options: Object,
  selected: Array
})

// =============================
// function
// =============================

// ref
const bar = ref()

// 清空active
const clearActiveCSS = () => {
  for (let i = 1; i < bar.value.children.length; i++) {
    bar.value.children[i].classList.remove('active')
  }
}

const switchBar = (e) => {
  // active样式
  clearActiveCSS()

  let index = 0
  for (index; index < bar.value.children.length; index++) {
    if (bar.value.children[index] === e.srcElement) {
      break
    }
  }

  for (let i = 0; i <= index; i++) {
    bar.value.children[i].classList.add('active')
  }

  // 传参
  emitEvent(e.target.innerText)
}

// =============================
// 传参
// =============================

const emit = defineEmits(['clickBar'])

const emitEvent = (newValue) => {
  emit(
    'clickBar',
    props.selected,
    valueMap[newValue] ? valueMap[newValue] : newValue
  )
}

// =============================
// defineExpose
// =============================

defineExpose({
  clearActiveCSS
})

// =============================
</script>

<template>
  <td style="padding: 0;">
    <div class="selectBar" ref="bar">
      <div class="selectBar-item" :class="{
        active: index === 0,
        'selectBar-item-0': index === 0
      }" v-for="(order, index) in options" :key="order" @click="switchBar">
        {{ order }}
      </div>
    </div>
  </td>
</template>

<style>
.selectBar {
  display: flex;
  height: 100%;
}

.selectBar-item {
  box-sizing: border-box;

  flex: 1;
  padding: 5px;
  border-right: 1px solid #d0edf2;

  display: flex;
  justify-content: center;
  align-items: center;

  text-align: center;
}

.selectBar-item-0 {
  min-width: 15%;
  max-width: 15%;
}

.active {
  background-color: skyblue;
}

:last-child {
  border-right: none;
}
</style>

<script setup>
// 通用
import { ref, computed, watch } from 'vue'

// 组件
import SelectBar from './SelectBar.vue'
const SelectBarRefs = ref([[], [], [], []])
const setSelectBarRef = (day, index) => {
  return (el) => {
    SelectBarRefs.value[day - 1][index] = el
  }
}

// Map
import { nameMap } from '../assets/nameMap.js'

// =============================
// 本战数据
// =============================

// 本战排位
const ranks = ['tierA', 'tierB', 'tierC', 'notRank']

// 勋章要求
const finalsLine = {
  tierA: {
    win: { crewHoners: [0, '1亿'], individualHoners: [0, '30万', '1000万'] },
    lose: {
      crewHoners: [0, '1000万', '1亿', '1.5亿', '2.5亿', '10亿', '30亿'],
      individualHoners: [
        0,
        '100万',
        '250万',
        '350万',
        '600万',
        '3000万',
        '9000万'
      ]
    }
  },
  tierB: {
    win: {
      crewHoners: [0, '3500万', '5000万'],
      individualHoners: [0, '20万', '36万', '48万']
    },
    lose: {
      crewHoners: [0, '500万', '3500万', '5000万'],
      individualHoners: [0, '50万', '90万', '120万']
    }
  },
  tierC: {
    win: { crewHoners: [0, '500万'], individualHoners: [0, '10万', '16万'] },
    lose: {
      crewHoners: [0, '180万', '500万'],
      individualHoners: [0, '15万', '40万']
    }
  },
  notRank: {
    individualHoners: [0, '40万']
  }
}

// =============================
// 选中数据
// =============================

const finalsSelected = ref({
  1: { rank: 'tierA', result: 'win', crewHoners: 0, individualHoners: 0 },
  2: { rank: 'tierA', result: 'win', crewHoners: 0, individualHoners: 0 },
  3: { rank: 'tierA', result: 'win', crewHoners: 0, individualHoners: 0 },
  4: { rank: 'tierA', result: 'win', crewHoners: 0, individualHoners: 0 }
})

const setSelected = ([day, thekey], newValue) => {
  finalsSelected.value[day][thekey] = newValue
}

// =============================
// function
// =============================

// 清空样式
const clearSelected = (day) => {
  // 清空数据
  finalsSelected.value[day].crewHoners = 0
  finalsSelected.value[day].individualHoners = 0

  // 清除样式
  if (finalsSelected.value[day].rank === 'notRank') {
    SelectBarRefs.value[day - 1][1].clearActiveCSS()
  } else {
    SelectBarRefs.value[day - 1][0].clearActiveCSS()
    SelectBarRefs.value[day - 1][1].clearActiveCSS()
  }
}

// 切换胜败场
const changeWin = (day) => {
  // 清空样式
  clearSelected(day)

  // 切换文字
  finalsSelected.value[day].result === 'win'
    ? (finalsSelected.value[day].result = 'lose')
    : (finalsSelected.value[day].result = 'win')
}

// =============================
// 计算
// =============================

// 统计数量
const finalsSum = ref({
  1: 0,
  2: 0,
  3: 0,
  4: 0
})

// 计算器
const finalsCounter = (selected) => {
  // computed
  const crewHoners = computed(() => {
    return selected.crewHoners
  })
  const individualHoners = computed(() => {
    return selected.individualHoners
  })

  // function
  let count = 0

  if (selected.rank === 'tierA') {
    // Rank A
    if (selected.result === 'win') {
      // Rank A - 胜场
      if (individualHoners.value >= 300000) {
        count += 60
      }
      if (crewHoners.value >= 100000000 && individualHoners.value >= 10000000) {
        count += 10
      }
    } else {
      // Rank A - 败场
      if (crewHoners.value >= 10000000 && individualHoners.value >= 1000000) {
        count += 25
      }
      if (crewHoners.value >= 100000000 && individualHoners.value >= 2500000) {
        count += 5
      }
      if (crewHoners.value >= 150000000 && individualHoners.value >= 3500000) {
        count += 5
      }
      if (crewHoners.value >= 250000000 && individualHoners.value >= 6000000) {
        count += 5
      }
      if (
        crewHoners.value >= 1000000000 &&
        individualHoners.value >= 30000000
      ) {
        count += 10
      }
      if (
        crewHoners.value >= 3000000000 &&
        individualHoners.value >= 90000000
      ) {
        count += 10
      }
    }
  } else if (selected.rank === 'tierB') {
    // Rank B
    if (selected.result === 'win') {
      // Rank B - 胜场
      if (individualHoners.value >= 200000) {
        count += 25
      }
      if (crewHoners.value >= 35000000 && individualHoners.value >= 360000) {
        count += 5
      }
      if (crewHoners.value >= 50000000 && individualHoners.value >= 480000) {
        count += 5
      }
    } else {
      // Rank B - 败场
      if (crewHoners.value >= 5000000 && individualHoners.value >= 500000) {
        count += 10
      }
      if (crewHoners.value >= 35000000 && individualHoners.value >= 900000) {
        count += 5
      }
      if (crewHoners.value >= 50000000 && individualHoners.value >= 1200000) {
        count += 5
      }
    }
  } else if (selected.rank === 'tierC') {
    // Rank C
    if (selected.result === 'win') {
      // Rank C - 胜场
      if (individualHoners.value >= 100000) {
        count += 10
      }
      if (crewHoners.value >= 5000000 && individualHoners.value >= 160000) {
        count += 5
      }
    } else {
      // Rank C - 败场
      if (crewHoners.value >= 1800000 && individualHoners.value >= 150000) {
        count += 5
      }
      if (crewHoners.value >= 5000000 && individualHoners.value >= 400000) {
        count += 5
      }
    }
  } else if (selected.rank === 'notRank') {
    // 圈外
    if (individualHoners.value >= 400000) {
      count += 5
    }
  }

  return count
}

// 监听
watch(
  finalsSelected.value[1],
  () => {
    finalsSum.value[1] = finalsCounter(finalsSelected.value[1])
  },
  { deep: true }
)
watch(
  finalsSelected.value[2],
  () => {
    finalsSum.value[2] = finalsCounter(finalsSelected.value[2])
  },
  { deep: true }
)
watch(
  finalsSelected.value[3],
  () => {
    finalsSum.value[3] = finalsCounter(finalsSelected.value[3])
  },
  { deep: true }
)
watch(
  finalsSelected.value[4],
  () => {
    finalsSum.value[4] = finalsCounter(finalsSelected.value[4])
  },
  { deep: true }
)

// =============================
// defineExpose
// =============================

defineExpose({
  finalsSum
})

// =============================
</script>

<template>
  <template v-for="day in 4" :key="day">
    <!-- 表头 -->
    <tr>
      <th colspan="2">
        <div style="display: flex; justify-content: center; gap: 5px">
          <span>{{ nameMap['finals'] }}-第{{ day }}天</span>

          <select v-model="finalsSelected[day].rank" @change="clearSelected(day)">
            <option v-for="rank in ranks" :key="rank" :value="rank">
              {{ nameMap[rank] }}
            </option>
          </select>

          <div class="isWin" v-if="finalsSelected[day].rank !== 'notRank'" @click="changeWin(day)">
            {{ nameMap[finalsSelected[day].result] }}
          </div>
        </div>
      </th>
      <th>获取勋章数</th>
    </tr>

    <!-- 表格 -->
    <template v-for="(options, label, index) in finalsSelected[day].rank === 'notRank'
      ? finalsLine[finalsSelected[day].rank]
      : finalsLine[finalsSelected[day].rank][finalsSelected[day].result]" :key="label">
      <tr>
        <!-- 左侧表头 -->
        <th>
          {{ nameMap[label] }}
        </th>

        <!-- 选项 td在组件里 -->
        <SelectBar :ref="setSelectBarRef(day, index)" :options="options" :selected="[day, label]"
          @clickBar="setSelected"></SelectBar>

        <!-- 勋章计算 每天本战各渲染一次 -->
        <td :rowspan="finalsSelected[day].rank === 'notRank' ? 1 : 2" style="text-align: center" v-if="index === 0">
          {{ finalsSum[day] }}
        </td>
      </tr>
    </template>
  </template>
</template>

<style scoped>
.isWin {
  width: 50px;
  border: 1px solid gray;
  background-color: #f1c938;
}
</style>

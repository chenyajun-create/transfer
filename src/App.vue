<script setup>
import { ref, computed } from 'vue'
//#region 左侧
// navigator
handleCopy()
function handleCopy() {
  let copy_text = '第一行需要复制的内容\r\n第二行需要复制的内容' //拿到想要复制的值

  // 使用Clipboard API复制文本到剪贴板中
  navigator.clipboard
    .writeText(copy_text)
    .then(() => {
      alert('复制成功！')
    })
    .catch((err) => {
      alert('复制失败！')
    })
}

const listLeft = ref([])

// 初始化数据
getListData()
function getListData() {
  let data = []
  for (let index = 1; index <= 10; index++) {
    data.push({
      key: index,
      label: 'option' + index,
    })
  }

  //对data进行处理 默认我们认为没有checked属性
  data.forEach((item) => {
    item.checked = false
  })
  listLeft.value = data
}

// 点击checkbox进行状态取反
function handleTransferLeft(currentIndex) {
  listLeft.value[currentIndex].checked = !listLeft.value[currentIndex].checked
}

// 左侧checked的数据
const currentCheckedLeft = computed(() => {
  return listLeft.value.filter((item) => item.checked)
})

// 左侧选中的比例数目
const currentSelectLeft = computed(() => {
  return currentCheckedLeft.value.length + '/' + listLeft.value.length
})

// 点击左箭头
function toLeft() {
  // 先找到右侧选中的数据
  const checkedLeftData = currentCheckedRight.value

  // 对数据进行深拷贝 因为这里修改了checked
  const deepCloneData = JSON.parse(JSON.stringify(checkedLeftData))
  deepCloneData.forEach((item) => {
    item.checked = false
  })

  // 这里筛选出来 右边没有选中的数据 等会直接赋值
  const noCheckedLeftData = listRight.value.filter((item) => !item.checked)

  // 把选中的数据添加到左边
  listLeft.value.push(...deepCloneData)
  // 对左边数据进行排序
  listLeft.value.sort((a, b) => a.key - b.key)

  // 将右边没有选中的数据赋值给右边
  listRight.value = noCheckedLeftData
}
//#endregion

//#region 右侧
const listRight = ref([])

// 右侧选中的数据
const currentCheckedRight = computed(() => {
  return listRight.value.filter((item) => item.checked)
})

// 右侧选中的比例数目
const currentSelectRight = computed(() => {
  return currentCheckedRight.value.length + '/' + listRight.value.length
})

// 点击checkbox进行状态取反
function handleTransferRight(currentIndex) {
  listRight.value[currentIndex].checked = !listRight.value[currentIndex].checked
}

// 点击右箭头
function toRight() {
  // 对选中数据进行深拷贝
  const deepCloneData = JSON.parse(JSON.stringify(currentCheckedLeft.value))
  // 把刚选择的数据状态进行清除
  deepCloneData.forEach((item) => {
    item.checked = false
  })
  // 把选中的数据添加到右边 需要把刚选择的数据状态进行清除
  listRight.value.push(...deepCloneData)
  // 对右边数据进行重新排序
  listRight.value.sort((a, b) => a.key - b.key)

  // 找到未选中数据 进行重新赋值
  const noCheckedLeftData = listLeft.value.filter((item) => !item.checked)
  listLeft.value = noCheckedLeftData
}

//#endregion
</script>
<template>
  <div style="width: 80%; height: 100%; margin: 0 auto; display: flex; justify-content: center; align-items: center">
    <main class="module-container" style="display: flex">
      <div class="module-wrapper">
        <div class="module-head">
          <span>list1</span>
          <span>{{ currentSelectLeft }}</span>
        </div>
        <div class="module-content">
          <div v-if="!listLeft.length" style="text-align: center">No data</div>
          <div class="module-item" v-for="(item, index) in listLeft" @click="handleTransferLeft(index)">
            <input style="width: 20px; height: 20px" type="checkbox" :checked="item.checked" />
            <span
              :style="{
                color: item.checked ? '#68b2ff' : '#000',
              }"
              >{{ item.label }}</span
            >
          </div>
        </div>
      </div>
      <div class="module-operate">
        <div class="module-arrow" :class="{ hoverStyle: !currentCheckedRight.length }" @click="toLeft">←</div>
        <div class="module-arrow" :class="{ hoverStyle: !currentCheckedLeft.length }" @click="toRight">→</div>
      </div>
      <div class="module-wrapper">
        <div class="module-head">
          <span>list2</span>
          <span>{{ currentSelectRight }}</span>
        </div>
        <div class="module-content">
          <div v-if="!listRight.length" style="text-align: center">No data</div>
          <div class="module-item" v-for="(item, index) in listRight" @click="handleTransferRight(index)">
            <input style="width: 20px; height: 20px" type="checkbox" :checked="item.checked" />
            <span
              :style="{
                color: item.checked ? '#68b2ff' : '#000',
              }"
              >{{ item.label }}</span
            >
          </div>
        </div>
      </div>
    </main>
  </div>
</template>
<style lang="scss" scoped>
@media (max-width: 600px) {
  .module-wrapper {
    width: 130px !important;
  }
}
.module-container {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.module-wrapper {
  width: 200px;
  flex-shrink: 0;
  .module-head {
    width: 100%;
    display: flex;
    justify-content: space-between;
    background-color: #78bbff;
    padding: 10px;
    box-sizing: border-box;
  }
  .module-content {
    width: 100%;
    border: 1px solid #000;
    height: 400px;

    box-sizing: border-box;
    .module-item {
      margin-top: 10px;
      margin-left: 10px;
      display: flex;
      align-items: center;
      span {
        display: inline-block;
        font-size: 20px;
      }
      &:hover span {
        color: #68b2ff !important;
        cursor: pointer;
      }
    }
  }
}
.module-operate {
  display: flex;
  gap: 10px;
  align-items: center;
  justify-content: center;
  margin: 0 10px;
  width: 200px;
  .module-arrow {
    width: 50px;
    height: 50px;
    background: #409eff;
    border-radius: 7px;
    font-size: 30px;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #fff;
    &:hover {
      cursor: pointer;
    }
  }
}
.hoverStyle {
  background-color: #a0cfff;
  opacity: 0.5;
  &:hover {
    cursor: not-allowed !important;
  }
}
</style>

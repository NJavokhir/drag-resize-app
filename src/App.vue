<template>
  <div class="container">
    <vue3-draggable-resizable
      v-for="(graph, index) in graphs"
      :key="graph.id"
      :x="graph.x"
      :y="graph.y"
      :w="graph.w"
      :h="graph.h"
      :minw="100"
      :minh="100"
      :parent="true"
      :active="true"
      @activated="activeId = graph.id"
      @dragging="(left, top) => onDragging(index, left, top)"
      @resizing="(left, top, width, height) => onResizing(index, left, top, width, height)"
      :style="{ border: '2px solid red' }"
    >
      <BarChart v-if="graphCharts[index]" :chart-data="graphCharts[index].data" />
    </vue3-draggable-resizable>
  </div>
</template>

<script setup>
import { reactive, ref } from 'vue'
import Vue3DraggableResizable from 'vue3-draggable-resizable'
import 'vue3-draggable-resizable/dist/Vue3DraggableResizable.css'
import BarChart from './components/BarChart.vue'

const gap = 10

const graphs = reactive([
  { id: 1, x: 0, y: 0, w: 300, h: 200 },
  { id: 2, x: 310, y: 0, w: 300, h: 200 },
  { id: 3, x: 620, y: 0, w: 300, h: 200 }
])

const graphCharts = [
  {
    id: 1,
    data: {
      labels: ['A', 'B', 'C', 'D'],
      datasets: [
        {
          label: 'Dataset 1',
          data: [20, 40, 60, 80],
          backgroundColor: '#42a5f5'
        }
      ]
    }
  },
  {
    id: 2,
    data: {
      labels: ['W', 'X', 'Y', 'Z'],
      datasets: [
        {
          label: 'Dataset 2',
          data: [50, 30, 70, 20],
          backgroundColor: '#66bb6a'
        }
      ]
    }
  },
  {
    id: 3,
    data: {
      labels: ['Q1', 'Q2', 'Q3', 'Q4'],
      datasets: [
        {
          label: 'Dataset 3',
          data: [15, 25, 35, 45],
          backgroundColor: '#ffa726'
        }
      ]
    }
  }
]

const activeId = ref(null)

function isOverlapping(a, b) {
  return !(
    a.x + a.w + gap <= b.x ||
    b.x + b.w + gap <= a.x ||
    a.y + a.h + gap <= b.y ||
    b.y + b.h + gap <= a.y
  )
}

function hasCollision(currentIndex, testRect) {
  return graphs.some((g, i) => {
    if (i === currentIndex) return false
    return isOverlapping(testRect, g)
  })
}

function onDragging(index, newX, newY) {
  const testRect = {
    x: newX,
    y: newY,
    w: graphs[index].w,
    h: graphs[index].h
  }

  if (!hasCollision(index, testRect)) {
    graphs[index].x = newX
    graphs[index].y = newY
  }
}

function onResizing(index, newX, newY, newW, newH) {
  const testRect = {
    x: newX,
    y: newY,
    w: newW,
    h: newH
  }

  if (!hasCollision(index, testRect)) {
    graphs[index].x = newX
    graphs[index].y = newY
    graphs[index].w = newW
    graphs[index].h = newH
  }
}
</script>

<style>
.container {
  width: 1000px;
  height: 500px;
  position: relative;
  background: #f0f0f0;
  margin: 30px auto;
  border: 1px solid #ccc;
}
</style>

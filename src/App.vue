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
      :active="activeId === graph.id"
      @activated="activeId = graph.id"
      @dragging="(left, top) => onDragging(index, left, top)"
      @resizing="(_, __, width, height) => onResizing(index, width, height)"
      @dragstop="() => saveBackup()"
      @resizestop="() => saveBackup()"
    >
      <div class="graph">Graph {{ index + 1 }}</div>
    </vue3-draggable-resizable>
  </div>
</template>

<script setup>
import { reactive, ref } from 'vue'
import Vue3DraggableResizable from 'vue3-draggable-resizable'
import 'vue3-draggable-resizable/dist/Vue3DraggableResizable.css'

const gap = 10
const graphs = reactive([
  { id: 1, x: 0, y: 0, w: 300, h: 200 },
  { id: 2, x: 310, y: 0, w: 300, h: 200 },
  { id: 3, x: 620, y: 0, w: 300, h: 200 }
])

const backup = reactive(JSON.parse(JSON.stringify(graphs)))
const activeId = ref(null)

function saveBackup() {
  graphs.forEach((g, i) => {
    backup[i] = { ...g }
  })
}

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
  const test = {
    x: newX,
    y: newY,
    w: graphs[index].w,
    h: graphs[index].h
  }

  if (hasCollision(index, test)) {
    graphs[index].x = backup[index].x
    graphs[index].y = backup[index].y
  } else {
    graphs[index].x = newX
    graphs[index].y = newY
  }
}

function onResizing(index, newW, newH) {
  const test = {
    x: graphs[index].x,
    y: graphs[index].y,
    w: newW,
    h: newH
  }

  if (hasCollision(index, test)) {
    graphs[index].w = backup[index].w
    graphs[index].h = backup[index].h
  } else {
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
.graph {
  width: 100%;
  height: 100%;
  background: #cce5ff;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
}
</style>

<script setup>
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
} from 'chart.js'
import { Bar } from 'vue-chartjs'
import { computed } from 'vue'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

const props = defineProps({
  chartData: Object,
  chartOptions: Object,
  chartType: String
})

const options = computed(() => props.chartOptions || {
  responsive: true,
  maintainAspectRatio: false
})
</script>

<template>
  <Bar :data="chartData" :options="options" />
</template>

<style scoped>
canvas {
  width: 100% !important;
  height: 100% !important;
  background-color: aquamarine;
  border: 1px solid red;
  cursor: all-scroll;
  display: inline-block;
}
</style>
<template>
  <div class="w-full max-w-3xl mx-auto p-4 bg-white rounded-lg shadow">
    <h2 class="text-xs text-gray-500 space-x-2.5 font-bold  mb-4">Signups by Date</h2>
    <Bar :data="chartData" :options="chartOptions" />
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { Bar } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
} from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

const props = defineProps({
  users: {
    type: Array,
    required: true
  }
})

const chartData = computed(() => {
  const counts = {}

  props.users.forEach(user => {
    const date = user.signupDate
    counts[date] = (counts[date] || 0) + 1
  })

  const labels = Object.keys(counts).sort()
  const data = labels.map(label => counts[label])

  return {
    labels,
    datasets: [{
      label: 'Signups',
      data,
      backgroundColor: '#4ea69a' 
    }]
  }
})

const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  scales: {
    y: {
      beginAtZero: true
    }
  }
}
</script>

<style scoped>
div {
  height: 300px;
}
</style>

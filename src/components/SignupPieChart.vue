<template>
  <div class="w-full max-w-md mx-auto">
    <Pie :data="chartData" :options="chartOptions" />
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { Pie } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  ArcElement,
} from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, ArcElement)

const props = defineProps({
  users: Array
})

const chartData = computed(() => {
  const sourceCounts = {}

  props.users.forEach(user => {
    if (user.source) {
      sourceCounts[user.source] = (sourceCounts[user.source] || 0) + 1
    }
  })

  const labels = Object.keys(sourceCounts)
  const data = Object.values(sourceCounts)

  return {
    labels,
    datasets: [
      {
        label: 'Signups by Source',
        data,
        backgroundColor: [
          '#36A2EB',
          '#FF6384',
          '#FFCE56',
          '#4BC0C0',
          '#9966FF'
        ]
      }
    ]
  }
})

const chartOptions = {
  responsive: true,
  plugins: {
    legend: {
      position: 'top', 
      labels: {
        boxWidth: 20,
        padding: 20
      }
    },
    title: {
      display: true,
      text: 'Signups by Source'
    }
  },
  layout: {
    padding: {
      top: 20,
      bottom: 0
    }
  }
}
</script>

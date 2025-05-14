<template>
  <div class="w-full mx-auto max-h-[40vh] flex justify-center">
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

// Register necessary Chart.js components
ChartJS.register(Title, Tooltip, Legend, ArcElement)

// Props
const props = defineProps({
  tasks: {
    type: Array,
    required: true,
  },
})

// Count tasks by status
const statusCounts = computed(() => {
  const counts = {}
  props.tasks.forEach(task => {
    const status = task.status || 'Unknown'
    counts[status] = (counts[status] || 0) + 1
  })
  return counts
})

// Chart data
const chartData = computed(() => {
  const labels = Object.keys(statusCounts.value)
  const data = Object.values(statusCounts.value)

  return {
    labels,
    datasets: [
      {
        label: 'Tasks per Status',
        data,
        backgroundColor: [
          '#36A2EB', // Backlog
          '#FF6384', // To Do
          '#FFCE56', // In Progress
          '#4BC0C0', // Done
          '#9966FF', // Cancelled
          '#FF9F40', // Other statuses
        ],
        hoverOffset: 8,
      },
    ],
  }
})

// Chart options
const chartOptions = {
  responsive: true,
  
  plugins: {
    title: {
      display: false,
      text: 'Tasks Grouped by Status',
    },
    tooltip: {
      backgroundColor: '#ffffff',
      titleColor: '#111827',
      bodyColor: '#1f2937',
      borderColor: '#d1d5db',
      borderWidth: 1,
      cornerRadius: 8,
      padding: 10,
    },
    legend: {
        position: 'bottom',
        labels: {
            usePointStyle: true,
            pointStyle: 'rectRounded', // Enables rounded square
            boxWidth: 12,
            boxHeight: 12,
            padding: 16,
            color: '#374151', // Tailwind gray-700
            font: {
                size: 14,
            },
        },
    },
    elements: {
      arc: {
        radius: 150, // Fixed radius in pixels
      },
    },

  },
}
</script>

<style scoped>
/* Optional: add your own styles here */
</style>
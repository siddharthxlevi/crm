<template>
  <div class="w-full  mx-auto max-h-[40vh] flex justify-center">
    <Pie :data="chartData" :options="chartOptions" class="w-full" />
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

// Count tasks by assignee
const assigneeCounts = computed(() => {
  const counts = {}
  props.tasks.forEach(task => {
    const assignee = task.assigned_to?.full_name || 'Unassigned'
    counts[assignee] = (counts[assignee] || 0) + 1
  })
  return counts
})

// Chart data
const chartData = computed(() => {
  const labels = Object.keys(assigneeCounts.value)
  const data = Object.values(assigneeCounts.value)

  return {
    labels,
    datasets: [
      {
        label: 'Tasks per Assignee',
        data,
        backgroundColor: [
          '#36A2EB',
          '#FF6384',
          '#FFCE56',
          '#4BC0C0',
          '#9966FF',
          '#FF9F40',
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
      text: 'Tasks Grouped by Assignee',
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

<template>
  <div class="w-full max-w-lg mx-auto h-[300px]">
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
  LinearScale,
} from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

const props = defineProps({
  tasks: {
    type: Array,
    required: true,
  },
})

const openTasksCounts = computed(() => {
  const counts = {}
  props.tasks.forEach(task => {
    if (!['Done', 'Cancelled'].includes(task.status)) {
      const assignee = task.assigned_to?.full_name || 'Unassigned'
      counts[assignee] = (counts[assignee] || 0) + 1
    }
  })
  return counts
})

const chartData = computed(() => {
  const labels = Object.keys(openTasksCounts.value)
  const data = Object.values(openTasksCounts.value)

  return {
    labels,
    datasets: [
      {
        label: 'Open Tasks',
        data,
        backgroundColor: 'rgba(59, 130, 246, 0.7)', // Tailwind blue-500 with opacity
        borderRadius: {
          topLeft: 8,
          topRight: 8,
          bottomLeft: 0,
          bottomRight: 0,
        },
        borderSkipped: 'bottom',
        hoverBackgroundColor: 'rgba(59, 130, 246, 0.9)',
      },
    ],
  }
})

const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  scales: {
    x: {
      ticks: {
        color: '#6b7280', // Tailwind gray-500
        font: { size: 12 },
      },
      grid: {
        display: false,
      },
    },
    y: {
      beginAtZero: true,
      ticks: {
        stepSize: 1,
        color: '#6b7280',
        font: { size: 12 },
      },
      grid: {
        color: '#e5e7eb', // Tailwind gray-200
        drawBorder: false,
      },
    },
  },
  plugins: {
    // legend: {
    //   display: false,
    // },
    title: {
      display: false,
      text: 'Open Tasks by Assignee',
      color: '#374151', // Tailwind gray-700
      font: {
        size: 16,
        weight: '600',
      },
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
  },
}
</script>

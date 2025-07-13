<template>
    <v-card class="pa-4 border-lg border-primary" max-width="1000" variant="elevated" elevation="4" rounded="xl">
        <!-- ✅ Toggle view type -->
        <v-select v-model="selectedMetric" :items="['Pain Level', 'Mood']" label="Symptom Metric" class="mb-4" />
        <Bar :data="chartData" :options="chartOptions" />
    </v-card>
</template>

<script setup>
import { ref, computed } from 'vue'
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

// ✅ Register required Chart.js components
ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

// ✅ Props for cycle data
const props = defineProps({
    cycles: {
        type: Array,
        required: true
    }
})

const selectedMetric = ref('Pain Level')

// ✅ Utility to group cycles by month
function groupByMonth(cycles) {
    const grouped = {}
    for (const cycle of cycles) {
        const date = new Date(cycle.startDate)
        const key = `${date.getFullYear()}-${(date.getMonth() + 1).toString().padStart(2, '0')}`
        if (!grouped[key]) grouped[key] = []
        grouped[key].push(cycle)
    }
    return grouped
}

// ✅ Generate chart data
const chartData = computed(() => {
    const grouped = groupByMonth(props.cycles)
    const labels = Object.keys(grouped).sort()

    let dataSet = []

    if (selectedMetric.value === 'Pain Level') {
        dataSet = labels.map(month => {
            const values = grouped[month].map(c => parseInt(c.symptoms.pain) || 0)
            return Math.round(values.reduce((a, b) => a + b, 0) / values.length)
        })
    } else if (selectedMetric.value === 'Mood') {
        const moodScale = { happy: 5, neutral: 3, sad: 1 }
        dataSet = labels.map(month => {
            const values = grouped[month].map(c => moodScale[c.symptoms.mood?.toLowerCase()] || 2)
            return Math.round(values.reduce((a, b) => a + b, 0) / values.length)
        })
    }

    return {
        labels,
        datasets: [
            {
                label: selectedMetric.value,
                data: dataSet,
                backgroundColor: '#FF80AB'
            }
        ]
    }
})

const chartOptions = {
    responsive: true,
    plugins: {
        legend: { display: true },
        title: {
            display: true,
            text: 'Monthly Symptom Overview'
        }
    },
    scales: {
        y: {
            beginAtZero: true
        }
    }
}
</script>

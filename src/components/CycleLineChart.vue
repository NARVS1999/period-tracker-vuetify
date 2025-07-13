<template>
    <v-card class="pa-4 border-lg border-primary" max-width="1000" variant="elevated" elevation="4" rounded="xl">
        <v-select v-model="selectedView" :items="['Cycle Length', 'Pain Level', 'Mood']" label="Select Data View"
            class="mb-4" />

        <Line :data="chartData" :options="chartOptions" />
    </v-card>
</template>

<script setup>
import { computed, ref, onMounted } from 'vue'
import { Line } from 'vue-chartjs'
import {
    Chart as ChartJS,
    Title,
    Tooltip,
    Legend,
    LineElement,
    PointElement,
    CategoryScale,
    LinearScale
} from 'chart.js'

// ✅ Register Chart.js components
ChartJS.register(Title, Tooltip, Legend, LineElement, PointElement, CategoryScale, LinearScale)

// ✅ Props for cycle data passed from parent
const props = defineProps({
    cycles: {
        type: Array,
        required: true
    }
})

const selectedView = ref('Cycle Length')

// ✅ Compute chart labels and data
const chartData = computed(() => {
    const labels = props.cycles.map((cycle, index) => `Cycle ${index + 1}`)

    let dataSet = []

    if (selectedView.value === 'Cycle Length') {
        dataSet = props.cycles.map(cycle => {
            const start = new Date(cycle.startDate)
            const end = new Date(cycle.endDate)
            const length = Math.ceil((end - start) / (1000 * 60 * 60 * 24))
            return length
        })
    } else if (selectedView.value === 'Pain Level') {
        dataSet = props.cycles.map(c => parseInt(c.symptoms.pain) || 0)
    } else if (selectedView.value === 'Mood') {
        // Assign numeric values to moods for visualization
        const moodScale = { happy: 5, neutral: 3, sad: 1 }
        dataSet = props.cycles.map(c => moodScale[c.symptoms.mood.toLowerCase()] || 2)
    }

    // ✅ Predict next period: show dotted line extension
    const predictionEnabled = selectedView.value === 'Cycle Length' && dataSet.length >= 2
    if (predictionEnabled) {
        const avgLength = Math.round(
            dataSet.reduce((sum, val) => sum + val, 0) / dataSet.length
        )
        labels.push('Next Cycle')
        dataSet.push(avgLength)
    }

    return {
        labels,
        datasets: [
            {
                label: selectedView.value,
                data: dataSet,
                borderColor: '#E91E63',
                backgroundColor: 'rgba(233,30,99,0.3)',
                fill: true,
                tension: 0.3,
                borderDash: predictionEnabled ? [0, 0, 0, 5] : [],
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
            text: 'Cycle Data Overview'
        }
    },
    scales: {
        y: {
            beginAtZero: true
        }
    }
}
</script>

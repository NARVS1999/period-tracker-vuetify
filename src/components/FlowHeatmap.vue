<!-- components/FlowHeatmap.vue -->
<template>
    <v-card class="pa-4 border-lg border-primary" max-width="1000" variant="elevated" elevation="4" rounded="xl">
        <!-- ✅ Month navigation -->
        <v-row justify="space-between" align="center" class="mb-4">
            <v-btn variant="text" color="primary" icon @click="prevMonth">
                <v-icon>mdi-chevron-left</v-icon>
            </v-btn>
            <div class="text-h6">{{ monthLabel }}</div>
            <v-btn variant="text" color="primary" icon @click="nextMonth">
                <v-icon>mdi-chevron-right</v-icon>
            </v-btn>
        </v-row>

        <!-- ✅ Calendar grid -->
        <div class="grid grid-cols-7 gap-2">
            <div v-for="day in daysInMonth" :key="day.date"
                class="h-10 w-10 rounded-md flex items-center justify-center text-sm cursor-pointer"
                :style="{ backgroundColor: getColor(day.intensity) }" v-tooltip="day.tooltip">
                {{ new Date(day.date).getDate() }}
            </div>
        </div>
    </v-card>
</template>

<script setup>
import { ref, computed } from 'vue'

// ✅ Props
const props = defineProps({
    cycles: {
        type: Array,
        required: true
    }
})

const currentDate = ref(new Date())

// ✅ Label for displayed month
const monthLabel = computed(() =>
    currentDate.value.toLocaleDateString('en-US', {
        year: 'numeric',
        month: 'long'
    })
)

// ✅ Generate days with flow intensity for heatmap
const daysInMonth = computed(() => {
    const year = currentDate.value.getFullYear()
    const month = currentDate.value.getMonth()
    const days = []

    const daysCount = new Date(year, month + 1, 0).getDate()

    for (let d = 1; d <= daysCount; d++) {
        const date = new Date(year, month, d).toISOString().split('T')[0]

        const match = props.cycles.find(cycle => {
            const start = new Date(cycle.startDate)
            const end = new Date(cycle.endDate)
            const target = new Date(date)
            return target >= start && target <= end
        })

        const flow = match?.flow || null
        const intensity = flow ? flow.toLowerCase() : 'none'
        const tooltip = match
            ? `Flow: ${flow}\nMood: ${match.symptoms.mood}\nPain: ${match.symptoms.pain}`
            : 'No record'

        days.push({ date, intensity, tooltip })
    }

    return days
})

// ✅ Map flow intensity to color
function getColor(flow) {
    switch (flow) {
        case 'heavy':
            return '#e91e63'
        case 'medium':
            return '#f48fb1'
        case 'light':
            return '#fce4ec'
        default:
            return '#f5f5f5'
    }
}

// ✅ Navigation functions
function prevMonth() {
    const date = new Date(currentDate.value)
    date.setMonth(date.getMonth() - 1)
    currentDate.value = date
}

function nextMonth() {
    const date = new Date(currentDate.value)
    date.setMonth(date.getMonth() + 1)
    currentDate.value = date
}
</script>

<style scoped>
.grid {
    display: grid;
}
</style>

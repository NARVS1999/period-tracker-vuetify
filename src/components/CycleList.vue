<!-- components/CycleList.vue -->
<template>
    <v-card variant="elevated" elevation="4" class="pa-4 ma-2" max-width="1000">
        <v-row dense>
            <v-col cols="12">
                <v-text-field v-model="search" label="Search by mood or flow" prepend-icon="mdi-magnify" clearable />
            </v-col>
            <v-col cols="12" md="6">
                <v-date-picker v-model="filterRange" range scrollable show-adjacent-months class="my-4" />
            </v-col>
            <v-col cols="12" md="6">
                <v-list v-if="filteredCycles.length">
                    <v-list-item v-for="(cycle, i) in filteredCycles" :key="i" @click="selectCycle(cycle)" class="mb-2">
                        <v-list-item-title>
                            {{ formatDate(cycle.startDate) }} - {{ formatDate(cycle.endDate) }}
                        </v-list-item-title>
                        <v-list-item-subtitle>
                            Mood: {{ cycle.symptoms.mood }} | Flow: {{ cycle.flow }}
                        </v-list-item-subtitle>
                    </v-list-item>
                </v-list>
                <v-alert v-else type="info" title="No records found" />
            </v-col>
        </v-row>





        <v-dialog v-model="dialog" width="500">
            <v-card>
                <v-card-title>Cycle Details</v-card-title>
                <v-card-text>
                    <p><strong>Start:</strong> {{ formatDate(selectedCycle.startDate) }}</p>
                    <p><strong>End:</strong> {{ formatDate(selectedCycle.endDate) }}</p>
                    <p><strong>Pain:</strong> {{ selectedCycle.symptoms.pain }}</p>
                    <p><strong>Mood:</strong> {{ selectedCycle.symptoms.mood }}</p>
                    <p><strong>Flow:</strong> {{ selectedCycle.flow }}</p>
                </v-card-text>
                <v-card-actions>
                    <v-spacer />
                    <v-btn text @click="dialog = false">Close</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-card>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const search = ref('')
const filterRange = ref({ start: null, end: null })
const cycles = ref([])
const selectedCycle = ref({})
const dialog = ref(false)

onMounted(() => {
    cycles.value = JSON.parse(localStorage.getItem('cycles') || '[]')
})

const filteredCycles = computed(() => {
    return cycles.value.filter((cycle) => {
        const matchesSearch =
            cycle.symptoms.mood.toLowerCase().includes(search.value.toLowerCase()) ||
            cycle.flow.toLowerCase().includes(search.value.toLowerCase())

        const inDateRange =
            (!filterRange.value.start || new Date(cycle.startDate) >= new Date(filterRange.value.start)) &&
            (!filterRange.value.end || new Date(cycle.endDate) <= new Date(filterRange.value.end))

        return matchesSearch && inDateRange
    })
})

function selectCycle(cycle) {
    selectedCycle.value = cycle
    dialog.value = true
}

function formatDate(date) {
    return new Date(date).toLocaleDateString()
}
</script>

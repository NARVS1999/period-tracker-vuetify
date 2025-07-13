<template>
    <v-card class="pa-4 border-lg border-primary" max-width="1000" variant="elevated" elevation="4" rounded="xl">
        <v-list v-if="cycles.length">
            <v-list-item v-for="(cycle, i) in cycles" :key="i" class="mb-2">
                <v-row dense>
                    <v-col cols="6" md="6">
                        <v-list-item-title>
                            {{ formatDate(cycle.startDate) }} - {{ formatDate(cycle.endDate) }}
                        </v-list-item-title>

                        <v-list-item-subtitle>
                            Mood: {{ cycle.symptoms.mood }} | Flow: {{ cycle.flow }}
                        </v-list-item-subtitle></v-col>
                    <v-col cols="6" md="6" class="d-flex ga-2 justify-end">
                        <v-btn icon color="blue" @click="$emit('edit', cycle, i)" size="small">
                            <v-icon>mdi-pencil</v-icon>
                        </v-btn>

                        <v-btn icon color="red" @click="confirmDelete(i)" size="small">
                            <v-icon>mdi-delete</v-icon>
                        </v-btn>
                    </v-col>
                </v-row>

            </v-list-item>
        </v-list>

        <v-dialog v-model="deleteDialog" width="400">
            <v-card>
                <v-card-title class="text-h6">Delete Cycle</v-card-title>
                <v-card-text>Are you sure you want to delete this cycle?</v-card-text>
                <v-card-actions>
                    <v-spacer />
                    <v-btn text @click="deleteDialog = false">Cancel</v-btn>
                    <v-btn color="red" text @click="deleteCycle">Delete</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-card>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
    cycles: Array
})

const emit = defineEmits(['edit', 'update'])

const deleteDialog = ref(false)
const deleteIndex = ref(null)

function confirmDelete(index) {
    deleteIndex.value = index
    deleteDialog.value = true
}

function deleteCycle() {
    const updated = [...props.cycles]
    updated.splice(deleteIndex.value, 1)
    localStorage.setItem('cycles', JSON.stringify(updated))
    emit('update', updated)
    deleteDialog.value = false
}

function formatDate(date) {
    return new Date(date).toLocaleDateString()
}
</script>

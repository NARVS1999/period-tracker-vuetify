<!-- components/CycleForm.vue -->
<template>
    <v-card variant="elevated" elevation="4" class="pa-4 ma-2" max-width="1000">
        <v-form v-model="isValid" @submit.prevent="saveCycle">
            <v-row dense>
                <v-col cols="12" md="6">
                    <v-date-picker v-model="cycle.startDate" label="Start Date" required />
                </v-col>
                <v-col cols="12" md="6">
                    <v-date-picker v-model="cycle.endDate" label="End Date" required />
                </v-col>
                <v-col cols="12" md="6">
                    <v-text-field v-model="cycle.symptoms.pain" label="Pain Level (1-10)" type="number"
                        :rules="[v => !!v || 'Required', v => (v >= 1 && v <= 10) || '1 to 10 only']" required />
                </v-col>
                <v-col cols="12" md="6">
                    <v-select v-model="cycle.flow" :items="['light', 'medium', 'heavy']" label="Flow Intensity"
                        required />
                </v-col>
                <v-col cols="12">
                    <v-text-field v-model="cycle.symptoms.mood" label="Mood" required />
                </v-col>

                <v-col cols="12" class="text-right">
                    <v-btn type="submit" color="primary" :disabled="!isValid">Save Cycle</v-btn>
                </v-col>
            </v-row>
        </v-form>
    </v-card>
</template>

<script setup>

const isValid = ref(false)

const cycle = ref({
    startDate: null,
    endDate: null,
    symptoms: {
        pain: '',
        mood: ''
    },
    flow: ''
})

const saveCycle = () => {
    if (!isValid.value) return
    const stored = JSON.parse(localStorage.getItem('cycles') || '[]')
    stored.push(cycle.value)
    localStorage.setItem('cycles', JSON.stringify(stored))
    alert('Cycle saved!')
    cycle.value = {
        startDate: null,
        endDate: null,
        symptoms: { pain: '', mood: '' },
        flow: ''
    }
}
</script>

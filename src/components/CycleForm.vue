<template>
    <v-card class="pa-4 border-lg border-primary" max-width="1000" variant="elevated" elevation="4" rounded="xl">
        <v-form v-model="isValid" @submit.prevent="handleSave">
            <v-row dense>
                <v-col cols="12" md="6">
                    <v-date-picker v-model="form.startDate" label="Start Date" />
                </v-col>
                <v-col cols="12" md="6">
                    <v-date-picker v-model="form.endDate" label="End Date" />
                </v-col>
                <v-col cols="12" md="6">
                    <v-text-field variant="solo-filled" rounded="lg" v-model="form.symptoms.pain" label="Pain Level"
                        type="number" prepend-inner-icon="mdi-heart-pulse" />
                </v-col>

                <v-col cols="12" md="6">
                    <v-select variant="solo-filled" rounded="lg" v-model="form.flow"
                        :items="['light', 'medium', 'heavy']" label="Flow Intensity" prepend-inner-icon="mdi-water" />
                </v-col>

                <v-col cols="12">
                    <v-text-field variant="solo-filled" rounded="lg" v-model="form.symptoms.mood" label="Mood"
                        prepend-inner-icon="mdi-emoticon" />
                </v-col>


                <v-col cols="12" class="text-right">
                    <v-btn type="submit" color="primary" :disabled="!isValid">
                        {{ props.index !== null ? 'Update' : 'Save' }}
                    </v-btn>
                </v-col>
            </v-row>
        </v-form>
    </v-card>
</template>

<script setup>
import { ref, reactive, watch } from 'vue'

const props = defineProps({
    cycleToEdit: Object,
    index: Number
})

const emit = defineEmits(['updated'])

const isValid = ref(true)

const form = reactive({
    startDate: null,
    endDate: null,
    symptoms: {
        pain: '',
        mood: ''
    },
    flow: ''
})

watch(
    () => props.cycleToEdit,
    (val) => {
        if (val) Object.assign(form, JSON.parse(JSON.stringify(val)))
    },
    { immediate: true }
)

function handleSave() {
    emit('updated', form, props.index)
}
</script>

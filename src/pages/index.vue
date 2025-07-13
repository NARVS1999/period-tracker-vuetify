<template>
  <v-container fluid class="pa-4" justify="center" align="center">
    <CycleForm :cycleToEdit="editingCycle" :index="editingIndex" @updated="saveCycle" />

    <v-divider class="my-4" />

    <CycleList :cycles="cycles" @edit="handleEdit" @update="handleUpdate" />

    <v-divider class="my-4" />

    <v-row dense>
      <v-col cols="12" sm="6">
        <CycleLineChart :cycles="cycles" />
      </v-col>
      <v-col cols="12" sm="6">
        <SymptomBarChart :cycles="cycles" />
      </v-col>
      <v-col cols="12">
        <FlowHeatmap :cycles="cycles" />
      </v-col>
    </v-row>

  </v-container>
</template>

<script setup>
// Auto-imported: no manual imports needed

const cycles = ref([])
const editingCycle = ref(null)
const editingIndex = ref(null)

onMounted(() => {

  cycles.value = JSON.parse(localStorage.getItem('cycles') || '[]')
})

function saveCycle(data, index) {
  if (index !== null) {
    cycles.value[index] = data
  } else {
    cycles.value.push(data)
  }
  localStorage.setItem('cycles', JSON.stringify(cycles.value))

  editingCycle.value = null
  editingIndex.value = null
}

function handleEdit(cycle, index) {
  editingCycle.value = { ...cycle }
  editingIndex.value = index
}

function handleUpdate(newCycles) {
  cycles.value = newCycles
  localStorage.setItem('cycles', JSON.stringify(newCycles))
}
</script>

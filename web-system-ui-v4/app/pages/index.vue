<template>
  <v-container fluid class="pa-6">
    <v-row class="align-center mb-6">
      <v-col cols="6">
        <h2 class="text-h5 font-weight-medium"><b>Suppliers Page</b></h2>
      </v-col>
      <v-col cols="6" class="text-right">
        <v-btn color="primary" @click="showDialog = true">+ Create</v-btn>
      </v-col>
    </v-row>

    <v-data-table
      :headers="headers"
      :items="suppliers"
      :items-per-page="itemsPerPage"
      class="elevation-2"
      density="comfortable"
    >
      <template #item.actions="{ item }">
        <v-btn color="primary" size="small" class="mr-2" @click="viewSupplier(item)">VIEW</v-btn>
        <v-btn color="error" size="small" @click="deleteSupplier(item.id)">DELETE</v-btn>
      </template>

      <template #no-data>
        <div class="pa-4 text-center text-grey">
          No suppliers yet. Click <b>+ Create</b> to add one.
        </div>
      </template>
    </v-data-table>

    <v-dialog v-model="showDialog" max-width="400px">
      <v-card>
        <v-card-title class="text-h6 font-weight-medium">Add Supplier</v-card-title>
        <v-card-text>
          <v-text-field v-model="newSupplier.name" label="Supplier Name" outlined dense />
          <v-text-field v-model="newSupplier.contact" label="Contact Number" outlined dense />
          <v-text-field v-model="newSupplier.address" label="Address" outlined dense />
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn text @click="showDialog = false">Cancel</v-btn>
          <v-btn color="primary" @click="saveSupplier">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'

const headers = [
  { text: 'ID', value: 'id' },
  { text: 'Name', value: 'name' },
  { text: 'Contact', value: 'contact' },
  { text: 'Address', value: 'address' },
  { text: 'Created At', value: 'createdAt' },
  { text: '', value: 'actions', sortable: false },
]

const suppliers = ref([])
const itemsPerPage = ref(10)
const showDialog = ref(false)
const newSupplier = ref({ name: '', contact: '', address: '' })


onMounted(() => {
  const saved = localStorage.getItem('suppliers')
  if (saved) suppliers.value = JSON.parse(saved)
})


watch(suppliers, (val) => {
  localStorage.setItem('suppliers', JSON.stringify(val))
}, { deep: true })


const saveSupplier = () => {
  if (!newSupplier.value.name.trim()) {
    alert('Please enter a supplier name.')
    return
  }

  const newId = suppliers.value.length > 0
    ? Math.max(...suppliers.value.map(s => s.id)) + 1
    : 1

  suppliers.value.push({
    id: newId,
    ...newSupplier.value,
    createdAt: new Date().toISOString(),
  })

  newSupplier.value = { name: '', contact: '', address: '' }
  showDialog.value = false
}

const viewSupplier = (item) => {
  alert(`Supplier: ${item.name}\nContact: ${item.contact}\nAddress: ${item.address}`)
}

const deleteSupplier = (id) => {
  if (confirm('Are you sure you want to delete this supplier?')) {
    suppliers.value = suppliers.value.filter(s => s.id !== id)
  }
}
</script>

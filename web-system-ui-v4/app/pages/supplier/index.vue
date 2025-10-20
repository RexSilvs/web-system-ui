<template>
  <v-container fluid class="pa-6">
    <v-row class="align-center mb-6">
      <v-col cols="6">
        <h2 class="text-h5 font-weight-medium"><b>Inventory Page</b></h2>
      </v-col>
      <v-col cols="6" class="text-right">
        <v-btn color="primary" @click="showDialog = true">+ Create</v-btn>
      </v-col>
    </v-row>

    <v-data-table
      :headers="headers"
      :items="inventory"
      :items-per-page="itemsPerPage"
      class="elevation-2"
      density="comfortable"
    >
      <template #item.actions="{ item }">
        <v-btn color="primary" size="small" class="mr-2" @click="viewItem(item)">VIEW</v-btn>
        <v-btn color="error" size="small" @click="deleteItem(item.id)">DELETE</v-btn>
      </template>

      <template #no-data>
        <div class="pa-4 text-center text-grey">
          No inventory items yet. Click <b>+ Create</b> to add one.
        </div>
      </template>
    </v-data-table>

   
    <v-dialog v-model="showDialog" max-width="450px">
      <v-card>
        <v-card-title class="text-h6 font-weight-medium">Add New Item</v-card-title>
        <v-card-text>
          <v-text-field v-model="newItem.name" label="Item Name" outlined dense />
          <v-text-field v-model="newItem.category" label="Category" outlined dense />
          <v-text-field v-model="newItem.supplier" label="Supplier" outlined dense />
          <v-text-field v-model="newItem.quantity" label="Quantity" type="number" outlined dense />
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn text @click="showDialog = false">Cancel</v-btn>
          <v-btn color="primary" @click="saveItem">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'


const headers = [
  { text: 'ID', value: 'id' },
  { text: 'Item Name', value: 'name' },
  { text: 'Category', value: 'category' },
  { text: 'Supplier', value: 'supplier' },
  { text: 'Quantity', value: 'quantity' },
  { text: 'Created At', value: 'createdAt' },
  { text: '', value: 'actions', sortable: true },
]


const inventory = ref([])
const itemsPerPage = ref(10)
const showDialog = ref(false)
const newItem = ref({
  name: '',
  category: '',
  supplier: '',
  quantity: '',
})


onMounted(() => {
  const saved = localStorage.getItem('inventory')
  if (saved) inventory.value = JSON.parse(saved)
})


watch(inventory, (val) => {
  localStorage.setItem('inventory', JSON.stringify(val))
}, { deep: true })


const saveItem = () => {
  if (!newItem.value.name.trim()) {
    alert('Please enter the item name.')
    return
  }

  const newId =
    inventory.value.length > 0
      ? Math.max(...inventory.value.map((i) => i.id)) + 1
      : 1

  inventory.value.push({
    id: newId,
    name: newItem.value.name.trim(),
    category: newItem.value.category.trim(),
    supplier: newItem.value.supplier.trim(),
    quantity: newItem.value.quantity || 0,
    createdAt: new Date().toLocaleString(),
  })

  newItem.value = { name: '', category: '', supplier: '', quantity: '' }
  showDialog.value = false
}

const viewItem = (item) => {
  alert(
    `Item: ${item.name}\nCategory: ${item.category}\nSupplier: ${item.supplier}\nQuantity: ${item.quantity}\nCreated At: ${item.createdAt}`
  )
}

const deleteItem = (id) => {
  if (confirm('Are you sure you want to delete this item?')) {
    inventory.value = inventory.value.filter((i) => i.id !== id)
  }
}
</script>

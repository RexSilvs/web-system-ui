<template>
  <v-container fluid class="pa-6">
    <v-row class="align-center mb-6">
      <v-col cols="6">
        <h2 class="text-h5 font-weight-medium"><b>Categories Page</b></h2>
      </v-col>
      <v-col cols="6" class="text-right">
        <v-btn color="primary" @click="showDialog = true">+ Create</v-btn>
      </v-col>
    </v-row>

    <v-data-table 
    :headers="headers" 
    :items="categories" 
    :items-per-page="itemsPerPage" 
    class="elevation-2"
    density="comfortable">
      
      <template #item.actions="{ item }">
        <v-btn color="primary" size="small" class="mr-2" @click="viewCategory(item)">View</v-btn>
        <v-btn color="error" size="small" @click="deleteCategory(item.id)">Delete</v-btn>
      </template>

      <template #no-data>
        <div class="pa-4 text-center text-grey">
          No Categories items yet. Click <b>+ Create</b> to add one.
        </div>
      </template>
      
    </v-data-table>

    <v-dialog v-model="showDialog" max-width="400px">
      <v-card>
        <v-card-title class="text-h6 font-weight-medium">
          Create New Category
        </v-card-title>
        <v-card-text>
          <v-text-field v-model="newCategoryName" label="Category Name" outlined dense autofocus />
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn text @click="showDialog = false">Cancel</v-btn>
          <v-btn color="primary" @click="saveCategory">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue'

const headers = [
  { text: 'ID', value: 'id', align: 'start' },
  { text: 'Name', value: 'name' },
  { text: 'Created At', value: 'createdAt' },
  { text: 'Actions', value: 'actions', sortable: false },
]

const categories = ref([])
const itemsPerPage = ref(10)
const showDialog = ref(false)
const newCategoryName = ref('')

onMounted(() => {
  const saved = localStorage.getItem('categories')
  if (saved) categories.value = JSON.parse(saved)
})

watch(categories, (val) => {
  localStorage.setItem('categories', JSON.stringify(val))
}, { deep: true })

const viewCategory = (item) => {
  alert(`Category: ${item.name}\nCreated At: ${item.createdAt}`)
}

const saveCategory = () => {
  if (!newCategoryName.value.trim()) {
    alert('Please enter a category name.')
    return
  }

  const newId =
    categories.value.length > 0
      ? Math.max(...categories.value.map((c) => c.id)) + 1
      : 1

  categories.value.push({
    id: newId,
    name: newCategoryName.value.trim(),
    createdAt: new Date().toLocaleString(),
  })

  newCategoryName.value = ''
  showDialog.value = false
}

const deleteCategory = (id) => {
  if (confirm('Are you sure you want to delete this category?')) {
    categories.value = categories.value.filter((c) => c.id !== id)
  }
}
</script>
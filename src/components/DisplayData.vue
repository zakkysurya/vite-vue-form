<template>
  <div>
    <h2>Form Submitted Data</h2>
    <ul>
      <li><strong>Username:</strong> {{ formData.username }}</li>
      <li><strong>Email:</strong> {{ formData.email }}</li>
      <li><strong>Password:</strong> {{ formData.password }}</li>
      <li><strong>Confirm Password:</strong> {{ formData.confirmPassword }}</li>
      <li>
        <strong>Photo Profile:</strong>
        <img
          v-if="formData.image"
          :src="imageURL"
          alt="Profile Image"
          style="max-width: 150px; max-height: 150px"
        />
      </li>
    </ul>
  </div>
</template>

<script setup>
import { computed } from 'vue'

// Props menerima data form yang disubmit
const props = defineProps({
  formData: Object
})

// Buat URL untuk gambar yang diunggah
const imageURL = computed(() => {
  return props.formData.image ? URL.createObjectURL(props.formData.image) : ''
})
</script>

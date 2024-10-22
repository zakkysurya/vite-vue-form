<template>
  <!-- Tampilkan form jika belum disubmit -->
  <div v-if="!isSubmitted">
    <form @submit.prevent="saveRegister">
      <label for="image">Photo Profile</label>
      <input type="file" @change="inputImage" name="image" accept=".jpg, .jpeg, .png" />
      <div v-if="!validatorForm.image.isValid" style="color: red">
        <p>{{ validatorForm.image.errorMessage }}</p>
      </div>
      <br />
      <label for="username">Username</label>
      <input
        type="text"
        v-model.trim="form.username"
        @change="validatorUsername"
        name="username"
        placeholder="Username here..."
      />
      <div v-if="!validatorForm.username.isValid" style="color: red">
        <p>{{ validatorForm.username.errorMessage }}</p>
      </div>
      <br />
      <label for="email">Email</label>
      <input
        type="text"
        v-model.trim="form.email"
        @change="validatorEmail"
        name="email"
        placeholder="Email here..."
      />
      <div v-if="!validatorForm.email.isValid" style="color: red">
        <p>{{ validatorForm.email.errorMessage }}</p>
      </div>
      <br />
      <label for="password">Password</label>
      <input
        :type="showHidePassword.password.showPassword ? 'text' : 'password'"
        v-model.trim="form.password"
        @change="validatorPassword"
        name="password"
        placeholder="password here..."
      />
      <button type="button" @click="toggleShowPassword('password')">
        {{ showHidePassword.password.textButton }}
      </button>
      <div v-if="!validatorForm.password.isValid" style="color: red">
        <p>{{ validatorForm.password.errorMessage }}</p>
      </div>
      <br />
      <label for="confirmPassword">Confirm Password</label>
      <input
        :type="showHidePassword.confirmPassword.showPassword ? 'text' : 'password'"
        v-model.trim="form.confirmPassword"
        @change="validatorConfirmPassword"
        name="confirmPassword"
        placeholder="Confirm password here..."
      />
      <button type="button" @click="toggleShowPassword('confirmPassword')">
        {{ showHidePassword.confirmPassword.textButton }}
      </button>
      <div v-if="!validatorForm.confirmPassword.isValid" style="color: red">
        <p>{{ validatorForm.confirmPassword.errorMessage }}</p>
      </div>
      <br />
      <button type="submit">Registrasi</button>
    </form>
  </div>
  <!-- Tampilkan komponen hasil jika form sudah disubmit -->
  <DisplayData v-else :formData="submittedData" />
</template>

<script setup>
import { reactive, ref } from 'vue'
import DisplayData from './components/DisplayData.vue'
// definisikan variabel untuk di dalam form
const form = reactive({
  image: '',
  username: '',
  email: '',
  password: '',
  confirmPassword: ''
})
// definisikan properti reaktif untuk validator
const validatorForm = reactive({
  image: {
    isValid: true,
    errorMessage: ''
  },
  username: {
    isValid: true,
    errorMessage: ''
  },
  email: {
    isValid: true,
    errorMessage: ''
  },
  password: {
    isValid: true,
    errorMessage: ''
  },
  confirmPassword: {
    isValid: true,
    errorMessage: ''
  }
})
// menangani input image
const inputImage = (event) => {
  // ambil file dari input
  const file = event.target.files[0]
  // jika file tidak kosong
  if (file) {
    // Memeriksa jenis MIME file
    const validMimeTypes = ['image/jpeg', 'image/png', 'image/jpg']
    if (validMimeTypes.includes(file.type)) {
      // Menandakan bahwa image valid
      validatorForm.image.isValid = true
      validatorForm.image.errorMessage = ''
      // Buat objek FileReader baru
      const reader = new FileReader()
      // Ketika file berhasil dibaca
      // reader.onload = (e) => {
      // Simpan hasil pembacaan file ke dalam form.image
      // form.image = e.target.result
      // }
      form.image = file
      // Baca file sebagai URL data
      reader.readAsDataURL(file)
    } else {
      // Menandakan bahwa image tidak valid
      validatorForm.image.isValid = false
      validatorForm.image.errorMessage = 'Ekstensi file hanya boleh berupa gambar (JPEG, PNG, JPG)'
      form.image = '' // Reset image jika tidak valid
      event.target.value = '' // Reset input file
    }
  } else {
    // Menandakan bahwa image tidak valid
    validatorForm.image.isValid = false
    validatorForm.image.errorMessage = 'File tidak boleh kosong'
    // Jika tidak ada file, reset image
    form.image = ''
  }
}
const validatorImage = () => {
  if (!form.image) {
    // Menandakan bahwa image tidak valid
    validatorForm.image.isValid = false
    validatorForm.image.errorMessage = 'File tidak boleh kosong'
  } else {
    // Menandakan bahwa image valid
    validatorForm.image.isValid = true
    validatorForm.image.errorMessage = ''
  }
}
// Validator untuk username
const validatorUsername = () => {
  // Menandakan bahwa username valid
  validatorForm.username.isValid = true
  validatorForm.username.errorMessage = ''
  if (form.username.length < 5) {
    // Menandakan bahwa username tidak valid
    validatorForm.username.isValid = false
    validatorForm.username.errorMessage = 'Username tidak boleh kurang dari 5 karakter'
  }
}
// Validator untuk email
const validatorEmail = () => {
  // validasi email kosong
  if (form.email.length < 1) {
    // Menandakan bahwa email tidak valid
    validatorForm.email.isValid = false
    validatorForm.email.errorMessage = 'Email tidak boleh kosong'
  } else {
    // Validator format email
    const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
    if (!emailPattern.test(form.email)) {
      // validator format email
      validatorForm.email.isValid = false
      validatorForm.email.errorMessage = 'Email belum sesuai format'
    } else {
      // Menandakan bahwa email valid
      validatorForm.email.isValid = true
      validatorForm.email.errorMessage = ''
    }
  }
}
// validator password
const validatorPassword = () => {
  // validasi jika password kosong
  if (form.password.length < 1) {
    validatorForm.password.isValid = false
    validatorForm.password.errorMessage = 'Password tidak boleh kosong'
  } else {
    // validasi karakter password
    const passwordPattern = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$/
    if (!passwordPattern.test(form.password)) {
      validatorForm.password.isValid = false
      validatorForm.password.errorMessage =
        'Password harus terdiri dari minimal 8 karakter dan mengandung huruf besar/kecil, angka, dan karakter khusus.'
    } else {
      validatorForm.password.isValid = true
      validatorForm.password.errorMessage = ''
    }
  }
}
// validator confirm password
const validatorConfirmPassword = () => {
  // validasi jika confirm password kosong
  if (form.confirmPassword.length < 1) {
    validatorForm.confirmPassword.isValid = false
    validatorForm.confirmPassword.errorMessage = 'Konfirmasi Password tidak boleh kosong'
  } else {
    // validasi jika konfirmasi password tidak sama dengan password
    if (form.confirmPassword !== form.password) {
      validatorForm.confirmPassword.isValid = false
      validatorForm.confirmPassword.errorMessage =
        'Konfirmasi Password tidak sesuai dengan password'
    } else {
      validatorForm.confirmPassword.isValid = true
      validatorForm.confirmPassword.errorMessage = ''
    }
  }
}
// param show hide password and confirm password
const showHidePassword = reactive({
  password: {
    textButton: 'Show',
    showPassword: false
  },
  confirmPassword: {
    textButton: 'Show',
    showPassword: false
  }
})
// Event untuk toggle show/hide password
const toggleShowPassword = (tag) => {
  // Mengakses password atau confirmPassword berdasarkan tag
  const field = showHidePassword[tag]

  // Mengubah state dari show/hide password
  field.showPassword = !field.showPassword

  // Mengatur teks tombol sesuai dengan kondisi
  field.textButton = field.showPassword ? 'hide' : 'show'
}

const isSubmitted = ref(false) // State untuk melacak apakah form sudah disubmit
const submittedData = ref(null) // State untuk menyimpan data yang disubmit

// Fungsi untuk validasi seluruh form
const validateForm = () => {
  validatorImage()
  validatorUsername()
  validatorEmail()
  validatorPassword()
  validatorConfirmPassword()
  // Return true jika semua validasi berhasil
  return (
    validatorForm.image.isValid &&
    validatorForm.username.isValid &&
    validatorForm.email.isValid &&
    validatorForm.password.isValid &&
    validatorForm.confirmPassword.isValid
  )
}
// Fungsi untuk menyimpan data registrasi
const saveRegister = () => {
  if (validateForm()) {
    // Jika validasi berhasil, submit form
    submittedData.value = { ...form }
    isSubmitted.value = true
  } else {
    // Jika validasi gagal, tampilkan pesan error
    alert('Data perlu diperbaiki...')
  }
}
</script>

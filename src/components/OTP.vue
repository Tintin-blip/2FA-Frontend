<template>
    <v-sheet >
      <v-form ref="form" v-model="valid" v-if="form">
        <alertContainer  :selectedAlert=selectedAlert />
        <v-title>Sign Up</v-title>
        <v-text-field
          v-model="email"
          :rules="emailRules"
          label="Email (admin@test.com)"
          required
          class="mb-4"
        ></v-text-field>
  
        <v-text-field
           v-model="password"
           :rules="passwordRules"
           label="Password (admin)"
           class="mb-4"
           type="password"
           required
        ></v-text-field>
  
        <div class="d-flex flex-column">
          <v-btn
            class="mt-6 mt-5"
            color="success"
            :loading="loading"
            @click="validate"
          >
            Sign Up
          </v-btn>
        </div>
      </v-form>
  
      <v-form ref="formOTP" v-if="otp">
      <div class="text-center">
        <img :src="qrCodeData"  width="150" alt="QR Code" class="mx-auto mb-4">
        <p class="mb-1">Please scan the QR code to retrieve your authentication code</p>
      </div>
      <v-otp-input v-model="otpInput" :error="error" @input="validateOtp"></v-otp-input>
      <v-btn :loading="loading" class="send-btn" color="success" @click="submitOtp">Send code</v-btn>
      </v-form>
      
      <div v-if="success" class="container">
        <h1 class="success-title">Authentication Succesfully with 2FA</h1>
      </div>

    </v-sheet>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import alertContainer from './alert.vue'
  import axios from 'axios'
  // DATA USER
  const idUser = ref();
  const email = ref('');
  const password = ref('');
  const valid = ref(false);
  const loading = ref(false)
  
  // FORM, OTP and data QR
  const form = ref(true);
  const otp = ref(false);
  const otpInput = ref(''); 
  const success = ref(false)
  const qrCodeData = ref('')

  // ERRORS
  const error = ref(false); 
  const selectedAlert= ref('')

  
  
  // Validate rules
  const emailRules = [
    v => !!v || 'Email is required',
    v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
  ];
  
  const passwordRules = [
    v => !!v || 'Password is required',
    v => (v && v.length >= 4) || 'Password must be at least 6 characters long',
  ];
  
  const validate = async() => {
    
    if (valid.value) {
    try {
      loading.value = true
      let data = { 
        email:email.value,
        password:password.value
      }
      
      const response = await axios.post(`${import.meta.env.VITE_APP_URL_BACKEND}/users/signup`,data)
      
      idUser.value = response.data.data.id_user
      qrCodeData.value = response.data.data.qr
      form.value = false
      loading.value = false
      otp.value = true

      }catch(error) { 
      selectedAlert.value = 'error-validate-fields'
      loading.value = false

    }
  } 
  };


const submitOtp = async() => {
  try{ 
    loading.value = true

    let data = {inputUserCode:otpInput.value}
    await axios.post(`${import.meta.env.VITE_APP_URL_BACKEND}/users/${idUser.value}/enable/2fa`,data);
    otp.value = false;
    success.value = true;
    loading.value = false
  }catch(err) {   
    otpInput.value = ''
    error.value = true;
    loading.value = false 
  }
};  
  </script>
  
  <style scoped>
 
  .alertContainer { 
    max-width: 200px; 
    
  }
  .v-sheet { 
    max-width: 500px; 
    margin:auto; 
    margin-top: 200px; 
    height: 400px;
    display: flex; /* Agregar display flex */
    flex-direction: column; /* Cambiar a columna */
    justify-content: center; /* Centrar verticalmente */
  }

  .v-form { 

    align-items: center;
    padding: 50px;
   
  }

  .container {
    height: 400px;
    display: flex;
    padding-top: 120px;
    text-align: center;
    background-color: #ffffff;
    border-radius: 8px;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

.success-title {
  
    font-size: 2.5rem;
    color: #28a745; /* Color verde para éxito */
}

.send-btn { 
  align-items: center; 
  margin-left: 135px;

}

@media only screen and (max-width: 500px) {
  .send-btn { 
    margin-left: 80px;
  }
}
  
  </style>
  
<template>
    <div class="alert-container">
    
      <v-alert 
        title="Error"
        text="Server error"
        type="error"
        v-if="selectedAlert == 'error-server'"
        class="custom-alert"
      ></v-alert>
  
      <v-alert 
        title="Error"
        text="Verify credentials"
        type="error"
        v-if="selectedAlert == 'error-validate-fields'"
        class="custom-alert"
      ></v-alert>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      selectedAlert: String
    }
  };
  </script>
  
  <style scoped>
  .alert-container {
    max-height: 60px;
  }
  
  .custom-alert {
   height: 60px;
  }
  </style>
  
    
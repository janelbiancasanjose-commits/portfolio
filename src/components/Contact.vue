<template>
    <!-- Contact Start -->
    <div class="container-fluid vh-100" id="last-part">
      <div class="row h-100 justify-content-center align-items-center"">

        <!-- Form/Text -->
        <div class="col-12 col-md-6 d-flex flex-column justify-content-start" id="contact-form">
          <div>
            <h3 id="contact" class="mb-3 text-center py-5">CONTACT ME</h3>
          </div>

          <div id="form-section" class="glass-form">
            <form @submit.prevent="submitForm">
            <div class="mb-3 text-start">
              <label for="name-box" id="contact-name" class="form-label">Name</label>
              <input v-model="name" type="text" class="form-control" placeholder="First Name M.I. Last Name" id="name-box" required>
            </div>

            <div class="mb-3 text-start">
              <label for="email-box" id="contact-email" class="form-label">Email</label>
              <input v-model="email" type="email" class="form-control" placeholder="Email" id="email-box" required>
            </div>

            <div class="mb-3 text-start">
              <label for="message-box" id="contact-message" class="form-label">Message</label>
              <textarea v-model="message" class="form-control" rows="3" id="message-box" placeholder="" req></textarea>
            </div>

            <div class="text-end">
              <button type="submit" class="submit-btn" :disabled="isLoading"> 
              {{ isLoading ? "Sending. . .": "Submit" }}
            </button>
            </div>

            <div class="d-flex justify-content-end mt-2">
              <div ref="recaptchaContainer"></div>
            </div>

            </form>
            </div>
          </div>
        </div>
      </div>
    <!-- Contact End -->
</template>

<script setup>

import { ref, onMounted } from "vue";

import { Notyf } from 'notyf';
import 'notyf/notyf.min.css';

const WEB3FORMS_ACCESS_KEY = "d9ccbfea-4550-4302-8d64-7d0920daee2c";
const name = ref("");
const email = ref("");
const message = ref("");

// Loading state
const isLoading = ref(false);

const notyf = new Notyf();

const SITE_KEY ='6LcOHn0sAAAAACBPV8aZ9HvLkYm_tAO69JPv5ACs';
const recaptchaContainer = ref(null);
const recaptchaWidgetId = ref(null);
const recaptchaToken = ref('');

// Callback called by reCAPTCHA when successful
function onRecaptchaSuccess(token) {
  recaptchaToken.value = token;
}

// Callback when expired
function onRecaptchaExpired() {
  recaptchaToken.value = '';
}

// Function to render the reCAPTCHA widget
function renderRecaptcha() {
  if (!window.grecaptcha) {
    console.error('reCAPTCHA not loaded');
    return;
  }

  recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
    sitekey: SITE_KEY,
    size: 'normal', // or 'compact'
    callback: onRecaptchaSuccess,
    'expired-callback': onRecaptchaExpired,
  });
}

// Function to reset reCAPTCHA 
function resetRecaptcha() {
  if (recaptchaWidgetId.value !== null) {
    window.grecaptcha.reset(recaptchaWidgetId.value);
    recaptchaToken.value = '';
  }
}



onMounted(() => {
  // This code waits for the Google reCAPTCHA library to load, then renders the reCAPTCHA widget using onMounted hook. 
  // The widget is rendered with grecaptcha.render(), which requires a sitekey. 
  // Callback functions handle success and expiration events. 
  // reCAPTCHA is reset upon form submission to clear the token.
  const interval = setInterval(() => {
    if (window.grecaptcha && window.grecaptcha.render) {
      renderRecaptcha();
      clearInterval(interval);
    }
  }, 100);

  onBeforeUnmount(() => {
    clearInterval(interval);
  });
});


// The submitForm() handler function sends the form data to web3forms and siplays succes or failure notifications toast
const submitForm = async () => {
    if(!recaptchaToken.value){
      notyf.error("PLease verify that you are not a robot");
      return;
    }

    isLoading.value = true;
    try{
      const response = await fetch("https://api.web3forms.com/submit", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Accept: "application/json",
        },
        body: JSON.stringify({
          access_key: WEB3FORMS_ACCESS_KEY,
          name: name.value,
          email: email.value,
          message: message.value,
        }),
      });

      const result = await response.json();
      if (result.success) {
        console.log(result);
        isLoading.value = false;

        notyf.success("Message Sent!");
      }
    }
    catch(error){
      console.log(error);

      isLoading.value = false;
      notyf.error("Failed to send message.")

    }

}

</script>
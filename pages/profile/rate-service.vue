<template>
  <v-app>
    <v-main class="bg-[#FFFFFF] dark:bg-[#191919] font-roboto" style="color: #323232 !important">
      <v-container class="pa-6 pt-12">
        <header class="d-flex justify-between mb-12 py-1 align-center">
          <nuxt-link to="/profile">
            <v-icon size="28" class="dark:text-[#FFFFFF]">mdi-chevron-left</v-icon>
          </nuxt-link>
          <p class="font-medium text-[20px] leading-[30px] tracking-normal dark:text-[#FFFFFF]">
            {{ translate("rate_our_sevice") }}
          </p>
          <div class="w-[28px]"></div> </header>

        <div class="w-full mb-[26px]">
          <h6 class="text-h6 font-medium px-0 mb-7 dark:text-[#FFFFFF]">
            {{ translate("how_was_your_experience") }}?
          </h6>

          <div class="mb-6 w-full">
            <label class="text-[#7F7F7F] text-sm mb-3 block">{{ translate("rating") }}</label>
            <div class="flex justify-center py-2">
              <StarRating v-model="rating" :size="48" />
            </div>
          </div>

          <div class="mb-6">
            <label class="text-sm dark:text-[#FFFFFF]">{{ translate("write_your_feedback") }}</label>
            <v-textarea
              v-model="message"
              :placeholder="translate('add_your_comment')"
              variant="outlined"
              rounded="lg"
              auto-grow
              rows="5"
              class="custom-textarea mt-3 text-[#7F7F7F]"
              hide-details="auto"
            ></v-textarea>
          </div>

          <v-btn
            block
            size="x-large"
            color="#3d6a7e"
            class="text-none text-white rounded-pill"
            flat
            :disabled="isSubmitting"
            :loading="isSubmitting"
            @click="submitFeedback"
          >
            {{ translate("sent") }}
          </v-btn>
        </div>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { useFeedbackStore } from '~/store/feedback'
import { useNuxtApp } from '#app'

const { $translate: translate, $alert } = useNuxtApp()
const feedbackStore = useFeedbackStore()
const router = useRouter()

// State
const rating = ref(0)
const message = ref('')
const isSubmitting = ref(false)

const submitFeedback = async () => {
  // Validation
  if (rating.value === 0) {
    $alert.error(translate('please_select_rating') || 'Please select a rating')
    return
  }
  
  if (!message.value.trim()) {
    $alert.error(translate('please_enter_message') || 'Please enter your feedback')
    return
  }

  const payload = {
    rating: rating.value,
    message: message.value.trim(),
  }

  try {
    isSubmitting.value = true
    await feedbackStore.createFeedback(payload)
    
    $alert.toast(translate('feedback_success') || 'Thank you for your feedback!')

    // Reset Form
    rating.value = 0
    message.value = ''
    
    // Optional: Redirect back after success
    // setTimeout(() => router.push('/profile'), 1500)
    
  } catch (err) {
    $alert.error(translate('feedback_error') || 'Error sending feedback. Please try again.')
  } finally {
    isSubmitting.value = false
  }
}
</script>
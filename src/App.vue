<template>
  <div class="max-w-2xl mx-4 sm:mx-auto mt-36">
    <h1 class="text-2xl sm:text-4xl font-bold text-center text-blue-600">Link Shortener</h1>

    <div class="mt-8 bg-gray-100 p-8 rounded-lg shadow-2xl shadow-slate-500 ">
      <label for="url" class="block sm:text-lg font-medium">Enter URL:</label>
      <input
        type="url"
        id="url"
        v-model="url"
        placeholder="Enter a valid URL"
        class="w-full p-3 mt-2 border border-gray-300 rounded-md text-sm sm:text-base"
      />

      <button @click="shortenUrl" class="w-full mt-4 bg-blue-600 text-white p-3 text-sm sm:text-base rounded-md">
        Shorten
      </button>

      <div v-if="shortenedUrl" class="mt-6">
        <p class="font-semibold text-sm sm:text-lg">Shortened URL:</p>
        <div class="flex items-center text-sm sm:text-base">
          <input
            type="text"
            :value="shortenedUrl"
            class="p-3 w-full border border-gray-300 rounded-md"
            readonly
          />
          <button @click="copyUrl" class="ml-4 p-3 bg-gray-200 rounded-md text-sm sm:text-base">Copy</button>
        </div>
      </div>

      <!-- Display error message -->
      <p v-if="error" class="text-sm sm:text-base text-red-500 mt-4">{{ error }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

// Define props that come from the backend (Laravel/Inertia)
const props = '39a1832afed336593452200299444e7f27205c2d'

// Vue reactive data
const url = ref('')
const shortenedUrl = ref('')
const error = ref('')

// Shorten the URL using Bitly API
const shortenUrl = async () => {
  if (!url.value) {
    error.value = 'Please enter a URL!'
    return
  }

  error.value = '' // Reset the error message

  try {
    // Make POST request to Bitly API
    const response = await axios.post(
      'https://api-ssl.bitly.com/v4/shorten',
      {
        long_url: url.value,
      },
      {
        headers: {
          Authorization: `Bearer ${props}`,
        },
      },
    )

    // Handle successful response
    shortenedUrl.value = response.data.link
  } catch (err) {
    console.error(err)
    error.value = 'Failed to shorten the URL. Please try again.'
  }
}

// Copy the shortened URL to clipboard
const copyUrl = () => {
  if (shortenedUrl.value) {
    navigator.clipboard.writeText(shortenedUrl.value).then(() => {
      alert('URL copied to clipboard!')
    })
  }
}
</script>

<style scoped>
/* TailwindCSS styling */
</style>

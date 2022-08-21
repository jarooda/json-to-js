<script setup lang="ts">
import BaseButton from '@/components/BaseButton.vue'
import BaseAlert from '@/components/BaseAlert.vue'

import { ref } from 'vue'

const baseModel = ref<string>('')
const resultModel = ref<string>('')
const alertText = ref<string>('')
const alertType = ref<string>('')
const isUsingDoubleQuote = ref<boolean>(true)
const space = ref<number>(2)

// @ts-ignore
const replacer = (key, value) => {
  return value
}

const convert = () => {
  try {
    alertType.value = ''
    const parsed = JSON.parse(baseModel.value)
    const stringify = JSON.stringify(parsed, replacer, space.value)

    // remove doublequote from properties
    let unquote = stringify.replace(/"([^"]+)":/g, '$1:')

    if (isUsingDoubleQuote.value) {
      // change " to '
      unquote = unquote.replace(/\"/g, "'")
    }

    resultModel.value = unquote
  } catch (error: any) {
    alertText.value = error.message
    alertType.value = 'error'
  }
}

const copyToClipboard = () => {
  navigator.clipboard.writeText(resultModel.value).then(() => {
    alertText.value = 'Result copied to clipboard'
    alertType.value = 'success'
  }, () => {
    alertText.value = 'There is error somewhere...'
    alertType.value = 'error'
  })
}
</script>

<template>
  <header>
    <h1 class="text-align-center">JSON to Javascript Converter</h1>
  </header>
  <main class="main-area">
    <textarea
      id="base"
      v-model="baseModel"
      placeholder="write something here..."
    />
    <div class="options-area">
      <base-alert v-if="alertType" :variant="alertType">
        {{ alertText }}
      </base-alert>
      <div>
        <label for="indent">Indent spacing?</label>
        <input id="indent" type="number" v-model="space" class="input-indent" />
      </div>
      <label for="doubletick" class="mt-8">
        <input id="doubletick" v-model="isUsingDoubleQuote" type="checkbox" />
        Convert Doublequote (") to Singlequote (') ?
      </label>
      <base-button
        id="convert-text"
        class="mt-8"
        block
        @click="convert"
      >
        Convert
      </base-button>
      <base-button
        id="convert-text"
        class="mt-8"
        block
        :disabled="!resultModel"
        @click="copyToClipboard"
      >
        Copy Result
      </base-button>
    </div>
    <textarea
      id="result"
      v-model="resultModel"
      placeholder="click convert button..."
      readonly
    />
  </main>
  <footer class="mt-16">
    <p class="text-align-center">JSON to Javascript converter by <a class="author" href="https://www.jaluwibowo.id/">Jalu Wibowo Aji</a></p>
    <p class="text-align-center"><a href="https://github.com/jarooda/json-to-js">Source Code</a></p>
  </footer>
</template>

<style scoped>
textarea {
  resize: none;
}

.text-align-center {
  text-align: center;
}

.main-area {
  width: 80vw;
  height: 80vh;
  display: grid;
  grid-template-columns: 1fr;
}

.options-area {
  padding: 16px;
  display: flex;
  flex-direction: column;
  order: 1;
}

.mt-8 {
  margin-top: 8px;
}

.mt-16 {
  margin-top: 16px;
}

.input-indent {
  margin-left: 8px;
  width: 100px;
}

.author {
  font-weight: 800;
}

@media (min-width: 1024px) {
  .main-area {
    grid-template-columns: 1fr 250px 1fr;
    height: 80vh;
  }

  .options-area {
    order: unset;
  }
}
</style>

<script setup lang="ts">
import { camelCase, constantCase, kebabCase, pascalCase, snakeCase } from 'change-case'
import BaseButton from '@/components/BaseButton.vue'
import BaseAlert from '@/components/BaseAlert.vue'

import { ref } from 'vue'

const baseModel = ref<string>('')
const resultModel = ref<string>('')
const alertText = ref<string>('')
const alertType = ref<string>('')
const quoteType = ref<string>('double')
const caseType = ref<string>('')
const space = ref<number>(2)

function changeCase(obj, caseType) {
  const keys = Object.keys(obj);

  for (const key of keys) {
    const newKey = (() => {
      switch (caseType) {
        case 'snake':
          return snakeCase(key);
        case 'kebab':
          return kebabCase(key);
        case 'camel':
          return camelCase(key);
        case 'pascal':
          return pascalCase(key);
        case 'constant':
          return constantCase(key);
        default:
          return key;
      }
    })();

    if (newKey !== key) {
      obj[newKey] = obj[key];
      delete obj[key];
    }

    if (typeof obj[newKey] === "object") {
      changeCase(obj[newKey], caseType);
    }
  }
  return obj;
}

function recursiveIterate(obj, caseType) {
  changeCase(obj, caseType);
}

const convert = () => {
  try {
    alertType.value = ''
    const parsed = JSON.parse(baseModel.value)
    recursiveIterate(parsed, caseType.value)
    const stringify = JSON.stringify(parsed, null, space.value)

    // remove doublequote from properties
    let unquote = stringify.replace(/"([^"]+)":/g, '$1:')

    if (quoteType.value === 'single') {
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

/**


{
  "key": "value",
  "key space": "value",
  "key-kebab": "value",
  "key value": {
    "key": "value",
    "key space": "value",
    "key-kebab": "value"
  },
  "key array": [ "value", "value", "value" ],
  "key array object": [ { "key": "value" }, { "key": "value" }, { "key": "value" } ],
  "key array object array": [ { "key": "value" }, { "key": "value" }, { "key": "value" } ],
  "key array object array object": [ { "key": "value" }, { "key": "value" }, { "key": "value" } ],
  "key array object array object array": [ { "key": {
    "key array object array object array": [ { "key": {
    "key array object array object array": [ { "key": {
    "array": [1,2,3]
  } }, { "key": "value" }, { "key": "value" } ]
  } }, { "key": "value" }, { "key": "value" } ]
  } }, { "key": "value" }, { "key": "value" } ]
}

 */
</script>

<template>
  <header>
    <h1 class="text-align-center">JSON to Javascript Converter</h1>
  </header>

  <!-- OPTIONS -->
  <div class="options-area">
    <div class="option-item">
      <label for="indent">Indent spacing?</label>
      <input id="indent" type="number" v-model="space" />
    </div>
    <div class="option-item">
      <label for="quote">Quote</label>
      <select id="quote" v-model="quoteType">
        <option value="single">Singlequote</option>
        <option value="double">Doublequote</option>
      </select>
    </div>
    <div class="option-item">
      <label for="quote">Case</label>
      <select id="quote" v-model="caseType">
        <option value="">default</option>
        <option value="snake">snake_case</option>
        <option value="kebab">kebab-case</option>
        <option value="camel">camelCase</option>
        <option value="pascal">PascalCase</option>
        <option value="constant">CONSTANT_CASE</option>
      </select>
    </div>
    <div class="buttons">
      <base-button
        id="convert-text"
        class="mt-8"
        @click="convert"
      >
        Convert
      </base-button>
      <base-button
        id="copy-text"
        class="mt-8"
        :disabled="!resultModel"
        @click="copyToClipboard"
      >
        Copy Result
      </base-button>
    </div>
    <base-alert v-if="alertType" :variant="alertType" class="mt-16">
      {{ alertText }}
    </base-alert>
  </div>

  <main class="main-area mt-16">
    <textarea
      id="base"
      v-model="baseModel"
      placeholder="write something here..."
    />
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

.options-area {
  margin: 0 auto;
  padding: 16px;
  display: flex;
  flex-direction: column;
  order: 1;
  background: white;
  color: black;
  width: max-content;
  border-radius: 8px;
}

.option-item {
  width: 500px;
  display: grid;
  grid-template-columns: 200px 300px;
  margin-bottom: 8px;
}

.buttons {
  display: flex;
  justify-content: center;
  gap: 8px;
}

.main-area {
  display: grid;
  grid-template-columns: 1fr;
  min-height: 450px;
}

.mt-8 {
  margin-top: 8px;
}

.mt-16 {
  margin-top: 16px;
}

.author {
  font-weight: 800;
}

@media (min-width: 1200px) {
  .main-area {
    width: 1024px;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
  }

  .options-area {
    order: unset;
  }
}
</style>

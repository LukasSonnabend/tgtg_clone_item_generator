<script setup lang="ts">
import { ref } from 'vue'
import dayjs from 'dayjs'

defineProps<{ msg: string }>()


const generated = ref('')

const header = "id;pickup_time;user_supplied_packaging;pickup_time_end;store_id;offer_type_id"

const copyToClipboard = () => {
  const el = document.createElement('textarea')
  el.value = generated.value
  document.body.appendChild(el)
  el.select()
  document.execCommand('copy')
  document.body.removeChild(el)
}

const generate = () => {
  const lines = []
  let dateStarter = dayjs().hour(20).toISOString()
  // check if dateStart is in the past
  if (dayjs(dateStarter).isBefore(dayjs())) {
    // if so, add 1 day
    dateStarter = dayjs(dateStarter).add(1, 'day').hour(20).toISOString()
  }


  let count = 1
  

  for (let day = 0; day <= 30; day++) { // day loop (10 days
    let dateStart = dayjs(dateStarter).add(day, 'day').toISOString()
  
    for (let i = 1; i <= 10; i++) { // store loop
    for (let j = 0; j < Math.random() * 11 % 8; j++) { // offer loop
      let date = dayjs(dateStart).hour(20).toISOString()
      const item = {
        id: count++,
        pickup_time: date,
        user_supplied_packaging: i % 2 === 0 ? 'true' : 'false',
        pickup_time_end: dayjs(date).add(1, 'hour').toISOString(),
        store_id: i,
        offer_type_id: i
      }
      lines.push(item)
    }
  }
  

  generated.value = header + '\n' + lines.reduce((acc, item) => {
    return acc + Object.values(item).join(";") + '\n'
  }, '')
  }
}

</script>

<template>
  <h1>{{ msg }}</h1>
  <div class="card buttons">
    <button type="button" @click="generate">Generate</button>
    <button type="button" v-if="generated.length > 0" @click="copyToClipboard">Copy to clipboard</button>
  </div>
  <textarea v-if="generated.length > 0" v-model="generated" disabled></textarea>
</template>

<style scoped>
.buttons {
  display: flex;
  gap: 2em;
  justify-content: center;
}

textarea {
  width: 100%;
  height: 300px;
}
</style>

<template>
  <div class="container mx-auto">
    <div class="flex flex-col bg-white">
      <div class="flex items-center p-3 border-b border-gray-200">
        <input class="text-lg font-semibold" v-model="username">
      </div>
      <div class="border-b border-gray-200 overflow-y-auto max-h-80 scrollarea">
        <div class="block py-3 border-b border-gray-200"
        v-for="message in messages" :key="message"
        >
          <div class="flex justify-between items-center">
            <strong class="mb-1">{{ message.username }}</strong>
          </div>
          <div class="mb-1 text-sm text-gray-600">{{ message.message }}</div>
        </div>
      </div>
    </div>
    <form @submit.prevent="submit">
      <input v-model="message" class="border w-full border-gray-300 rounded-md px-4 py-2 focus:outline-none focus:border-blue-500" placeholder="write a message"/>
    </form>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import Pusher from 'pusher-js'
export default {
  setup() {
    const username = ref('username');
    const messages = ref([])
    const message = ref('')

    onMounted(() => {
      Pusher.logToConsole = true;

      const pusher = new Pusher('794e484d5d330536bd95', {
        cluster: 'ap2'
      });

      const channel = pusher.subscribe('chat');
      channel.bind('message', data => {
        messages.value.push(data)
      });
    })

    const submit = async () => {
      await fetch('http://127.0.0.1:8000/api/messages', {
        method: 'POST',
        headers: {'Content-type': 'application/json'},
        body: JSON.stringify({
          username: username.value,
          message: message.value
        })
      })

      message.value = ''
    }

    return {
      username,
      messages,
      message,
      submit
    }
  }
}
</script>

<style>
.scrollarea{
  min-height: 500px;
}
</style>
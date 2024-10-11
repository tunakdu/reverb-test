<template>
  <div class="h-screen flex flex-col">
      <header class="bg-gray-800 text-white p-4 flex justify-between">
          <h1 class="text-xl font-bold">Chat Application</h1>
          <div class="flex items-center">
              <button
                  class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
              >
                  New Message
              </button>
              <div class="relative">
                  <button
                      class="bg-gray-900 hover:bg-gray-700 text-white font-bold py-2 px-4 rounded"
                  >
                      Notifications
                  </button>
                  <span
                      class="absolute top-0 right-0 bg-red-500 text-white rounded-full px-2 py-1 text-xs font-bold"
                  >
                      3
                  </span>
              </div>
              <div class="relative">
                  <button
                      class="bg-gray-900 hover:bg-gray-700 text-white font-bold py-2 px-4 rounded"
                  >
                      Settings
                  </button>
                  <span
                      class="absolute top-0 right-0 bg-red-500 text-white rounded-full px-2 py-1 text-xs font-bold"
                  >
                      1
                  </span>
              </div>
          </div>
      </header>
      <main class="flex-grow overflow-y-auto p-4">
          <div class="flex flex-col space-y-4">
              <!-- Mesajı gönderen kişi oturum açan kullanıcı mı değil mi kontrol ediyoruz -->
              <div v-for="message in messages" :key="message.id" class="flex"
                   :class="message.sender_id === currentUser.id ? 'justify-end' : 'justify-start'">
                  <div
                      :class="[
                          message.sender_id === currentUser.id
                              ? 'bg-blue-100'
                              : 'bg-gray-200',
                      ]"
                      class="max-w-md p-4 rounded-lg"
                  >
                      <p class="text-gray-800">{{ message.text }}</p>
                  </div>
              </div>
          </div>
      </main>
      <footer class="bg-gray-800 text-white p-4 flex">
          <input
              type="text"
              v-model="newMessage"
              class="flex-grow bg-gray-900 text-white p-2 rounded"
              placeholder="Type your message..."
          />
          <button
              @click="sendMessage"
              class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
          >
              Send
          </button>
      </footer>
  </div>
</template>

<script setup>
import { onMounted,ref } from "vue";
import axios from "axios";

const props = defineProps({
  friend: {
    type: Object,
      required: true
  },
  currentUser: {
    type: Object,
      required: true
  }
});

const messages = ref([]);

const newMessage = ref("");

const sendMessage = () => {
    if (newMessage.value.trim() !== "") {

      axios.post(`/messages/${props.friend.id}`, {

        message: newMessage.value,
        
      })
        .then(response => {
          messages.value.push(response.data);
          newMessage.value = "";
        });

    }
};

onMounted(() => {
  axios.get(`/messages/${props.friend.id}`)
    .then(response => {
      messages.value = response.data;
    });
});
</script>

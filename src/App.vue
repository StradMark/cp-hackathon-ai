<template>
  <div>
    <input type="text" v-model="query" />
    <button @click="interactWithChat(query)">Chat</button>

    <div :key="data.query" v-for="data in chatData">
      <div>Question: {{ data.query }}</div>
      <div>Response: {{ data.response }}</div>
    </div>
  </div>
</template>

<script setup async>
import { ref } from "vue";

const query = ref('');
let chatData = ref([]);

const apiKey = process.env.VUE_APP_API_KEY;

import { ChatOpenAI } from "@langchain/openai";

const chatModel = new ChatOpenAI({
  openAIApiKey: apiKey
});

async function interactWithChat(query) {
  try {
    const data = await chatModel.invoke(query);
    chatData.value.push({
      query: query,
      response: data.content
    })
    console.log("Response:", data);
  } catch (error) {
    console.error("Error:", error);
  }
}
</script>
<style>
  *, *::before, *::after {
    box-sizing: border-box;
  }

  html, body, #app {
    height: 100%;
    font-family: Arial, Helvetica, sans-serif;
  }

  body {
    margin: 0;
  }

  .chat-main {
    display: flex;
    flex-direction: column;
    width: 100%;
    height: 100%;
    font-size: 1.25em;
  }
  
  .chat-messages {
    display: flex;
    flex-direction: column;
    padding: 8px;
    width: 100%;
    max-height: 100%;
    flex-grow: 1;
    gap: 10px;
    overflow-y: auto;
    background-color: beige;
  }

  .chat-prompt {
    display: flex;
    flex-direction: row;
    gap: 10px;
    background-color: azure;
    padding: 8px;
  }

  .chat-prompt input {
    font-size: 1.25em;
  }

  .chat-message {
    border-radius: 10px;
    background-color: aqua;
    width: fit-content;
    padding: 8px;
    box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
  }

  .chat-message-ai {
    background-color: aliceblue;
    margin-left: auto;
  }

  .chat-message-author {
    display: block;
    font-size: 0.7em;
    margin-bottom: 6px;
    opacity: 0.6;
  }

  .chat-message-ai .chat-message-author {
    text-align: end;
  }

  .chat-input-text {
    flex-grow: 1;
  }

</style>

<template>
  <div class="chat-main">
    <div ref="messagesList" class="chat-messages">
      <template :key="data.query" v-for="data in chatData">
        <div class="chat-message">
          <div class="chat-message-author">You</div>
          <div>{{ data.query }}</div>
        </div>
        <div class="chat-message chat-message-ai">
          <div class="chat-message-author">AI</div>
          <div>{{ data.response }}</div>
        </div>
      </template>
    </div>

    <form @submit="onSubmitQuery($event, query)">
      <div class="chat-prompt">
          <input class="chat-input-text" type="text" v-model="query" />
          <input type="submit" value="Chat">
      </div>
    </form>
  </div>
</template>

<script setup async>
import { ref } from "vue";

const query = ref('');
const messagesList = ref(null);
let chatData = ref([]);

const apiKey = process.env.VUE_APP_API_KEY;

import { ChatOpenAI } from "@langchain/openai";

const chatModel = new ChatOpenAI({
  openAIApiKey: apiKey
});

function onSubmitQuery(event, submittedQuery) {
  event.preventDefault();

  query.value = '';

  if (submittedQuery.startsWith("Test:")) {
    setTimeout(() => {
      chatData.value.push({
        query: submittedQuery,
        response: "Example response"
      });
      scrollToBottomOfMessages();
    }, 150);
  } else {
    interactWithChat(query);
  }
}

async function interactWithChat(query) {
  try {
    const data = await chatModel.invoke(query);
    chatData.value.push({
      query: query,
      response: data.content
    })
    scrollToBottomOfMessages();
    
    console.log("Response:", data);
  } catch (error) {
    console.error("Error:", error);
  }
}

function scrollToBottomOfMessages() {
    setTimeout(() => {
      messagesList.value.scrollTop = messagesList.value.scrollHeight;
    }, 50);
}
</script>
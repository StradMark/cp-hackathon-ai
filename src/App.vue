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

  .chat-message-error {
    background-color: coral;
    color: white;
    margin-left: auto;
  }

  .chat-message-author {
    display: block;
    font-size: 0.7em;
    font-weight: bold;
    margin-bottom: 6px;
    opacity: 0.6;
  }

  .chat-message-ai .chat-message-author,
  .chat-message-error .chat-message-author {
    text-align: end;
  }

  @keyframes loading-pulse {
    0% { opacity: 0.25; }  
    50% { opacity: 0.6; }
    100% { opacity: 0.25; }
  }

  .chat-message-loading {
    animation: loading-pulse 0.8s infinite alternate;
  }

  .chat-input-text {
    flex-grow: 1;
  }

</style>

<template>
  <div class="chat-main">
    <div ref="messagesList" class="chat-messages">
      <template :key="data.message" v-for="data in chatData">
        <div v-bind:class="data.classes">
          <div class="chat-message-author">{{ data.author }}</div>
          <div>{{ data.message }}</div>
        </div>
      </template>
      <template v-if="isAiBusy">
        <div class="chat-message chat-message-ai chat-message-loading">
          <div>AI is responding...</div>
        </div>
      </template>
    </div>

    <form @submit="onSubmitQuery($event)">
      <div class="chat-prompt">
          <input class="chat-input-text" type="text" v-model="query" />
          <input :disabled="isAiBusy" type="submit" value="Chat">
      </div>
    </form>
  </div>
</template>

<script setup async>
import { ref } from "vue";

const messagesList = ref(null);

const query = ref('');
let chatData = ref([]);
let isAiBusy = ref(false);

const apiKey = process.env.VUE_APP_API_KEY;

import { ChatOpenAI } from "@langchain/openai";

const chatModel = new ChatOpenAI({
  openAIApiKey: apiKey
});

function onSubmitQuery(event) {
  event.preventDefault();

  if (isAiBusy.value) return;

  let submittedQuery = query.value;

  query.value = '';

  addChatMessage("query", submittedQuery);

  if (submittedQuery.startsWith("#")) {
    isAiBusy = true;
    setTimeout(() => {
      addChatMessage("response", "Example response");
      isAiBusy = false;
    }, 1500);

    return;
  } else {
    interactWithChat(submittedQuery);
  }
}

async function interactWithChat(submittedQuery) {
  isAiBusy = true;
  try {
    console.log("Query is " + submittedQuery);
    const data = await chatModel.invoke(submittedQuery);
    addChatMessage("response", data.content);
    isAiBusy = false;
    console.log("Response:", data);
  } catch (error) {
    addChatMessage("error", "Error message");
    isAiBusy = false;
    console.error("Error:", error);
  }
}

function addChatMessage(type, message) {
  let newMessage = {
    type: type,
    message: message,
    classes: { "chat-message": true },
    author: ""
  };

  switch (type) {
    case "query":
      newMessage.author = "You";
      break;
    case "response":
      newMessage.author = "AI";
      newMessage.classes["chat-message-ai"] = true;
      break;
    case "error":
      newMessage.author = "AI";
      newMessage.classes["chat-message-error"] = true;
      break;
    default:
      throw new Error(`Invalid message type "${type}"`);
  }

  chatData.value.push(newMessage);

  setTimeout(() => {
    messagesList.value.scrollTop = messagesList.value.scrollHeight;
  }, 50);
}

</script>
<template>
Henk
</template>

<script setup>
import { ChatOpenAI } from "@langchain/openai";

const chatModel = new ChatOpenAI({
  openAIApiKey: "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"
});

console.log(chatModel);
async function interactWithChat(message) {
  try {
    const response = await chatModel.invoke(message);
    console.log("Response:", response);
  } catch (error) {
    console.error("Error:", error);
  }
}
const response = await interactWithChat("What is 1 + 1?")

console.log(response);
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

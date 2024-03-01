<template>
  <div>
    <input type="text" v-model="message" />
    <button @click="callAPI(message)">Prompt</button>
  </div>
</template>

<script setup async>
import { ref } from "vue";
import axios from 'axios';

const message = ref('');


// Make a request for a user with a given ID
function callAPI(message) {
  console.log(message);
  axios.get('/user?ID=12345')
  .then(function (response) {
    // handle success
    console.log(response);
  })
  .catch(function (error) {
    // handle error
    console.log(error);
  })
  .finally(function () {
    // always executed
  });
}


// import { ChatOpenAI } from "@langchain/openai";

// const chatModel = new ChatOpenAI({
//   openAIApiKey: "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"
// });

// async function interactWithChat(message) {
//   try {
//     const response = await chatModel.invoke(message);
//     console.log("Response:", response);
//   } catch (error) {
//     console.error("Error:", error);
//   }
// }
</script>
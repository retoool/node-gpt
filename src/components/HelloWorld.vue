<template>
  <div class="chat-container">
    <div class="chat-history">
      <div v-for="message in messages" :key="message.id" class ="message-box" :class="message.type" v-html="message.text"></div>
    </div>
    <div class="chat-input">
      <input type="text" v-model="input" @keyup.enter="gptRequest" placeholder="请输入内容" />
      <button v-on:click="gptRequest()">发送</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      input: "",
      messages: [],
      id:0
    }
  },
  methods: {
    
    gptRequest() {
      if (this.input == "") {
        return
      }
      const data = JSON.stringify({
        "model": "gpt-3.5-turbo",
        "messages": [
          {
            "role": "user",
            "content": this.input
          }
        ]
      });
      this.messages.push({
        id: this.id++,
        text: this.input,
        type:"sent",
      })
      this.input = ""
      const config = {
        method: 'post',
        url: 'https://api.openai.com/v1/chat/completions',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': 'Bearer apikey'
        },
        data: data
      };

      axios(config)
        .then(response => {
          console.log(response);
          var result = response.data.choices[0].message.content;
          if (result.substr(0, 2) == "\n\n") {
            result = result.substr(2,result.length-2)
          }
          result = result.replace(/\n/g, '<br>')
          this.messages.push({
            id: this.id++,
            text: result,
            type:"received",
          })
        })
        .catch(error => {
          console.log(error);
          this.messages.push({
            id: this.id++,
            text: error,
            type:"received",
          })
        });
    },


  },
}
</script>
<style scoped>
.chat-container {
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  position: fixed;
  background-color: #1d082b;
}

.chat-history {
  overflow-y: auto;
  /* position: fixed; */
  /* top: 20%; */
  height: 90%;
  /* width: 100%; */
  /* justify-content: center; */
}

.message-box {
  width: auto;
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 2px 2px 5px rgba(0, 0, 0, .1);
  margin: 10px;
  padding: 10px;
}

.sent { 
  background-color: rgb(125, 63, 124); 
  align-self: flex-end; 
  color: antiquewhite;
}

.received { 
  color: rgb(1, 0, 0);
  background-color: rgb(189, 180, 189); 
} 
.chat-input {
  position: fixed;
  bottom: 0%;
  height: 10%;
  left: 0;
  right: 0;
  margin: auto;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #431650;
}

input[type="text"] {
  font-size: large;
  width: 50%;
  height: 40%;
  padding: 10px;
  border-radius: 10px;
  border: none;
  outline: none;
  box-shadow: 2px 5px 5px rgba(19, 31, 74, 0.1);
  margin-right: 10px;
  background-color: rgb(189, 180, 189);
}

button {
  height: 60%;
  width: 80px;
  font-size: large;
  padding: 5px;
  border-radius: 20px;
  border: none;
  outline: none;
  background-color: #896fd8;
  color: #f0daff;
  cursor: pointer;
  transition: background-color .2s;
}

button:hover {
  background-color: #25c5a0;
  color:antiquewhite;
}
</style>
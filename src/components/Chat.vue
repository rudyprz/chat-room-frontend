<template>
    <div class="text-center">
        <div class="text-lg text-gray-500">
            Enter a username to chat:
        </div>
        <input
            type="text"
            class="text-center shadow-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-transparent mt-2 px-2 py-2 sm:text-sm border border-gray-300 rounded-lg"
            placeholder="Your username"
            v-model="username"
        />
        <br>
        <span v-if="errorName" class="text-red-500 text-xs" >{{errorName}}</span>
        <div ref="refChat" style="overflow-y: auto" class="container h-96 max-h-96 my-6 sm:mx-6 bg-white border-solid border-2 border-blue-400 rounded shadow-lg p-4 px-4 md:p-8 sm:p-8 mb-6">

            <div v-for="item in chatMessages" :key="item._id" >
                <div v-if="item.sessionID === sessionID" class="mt-2">
                    <div class="text-right ml-1 text-sm text-gray-500">{{item.username}} says:</div>
                    <div class="flex flex-row-reverse">
                        <div class="max-w-max text-right my-1 py-1 px-2 bg-white border-solid border-2 border-blue-500 rounded-full text-blue-800">{{item.text}}</div>
                    </div>
                    <div class="text-right ml-1 text-xs text-gray-400">{{convertDate(item.createdAt)}}</div>
                </div>

                <div v-else class="mt-2">
                    <div class="text-left ml-1 text-sm text-gray-500">{{item.username}} says:</div>
                    <div class="max-w-max text-left my-1 py-1 px-2 bg-white border-solid border-2 border-purple-500 rounded-full text-purple-800">{{item.text}}</div>
                    <div class="text-left ml-1 text-xs text-gray-400">{{convertDate(item.createdAt)}}</div>
                </div>

            </div>
        </div>
        <div class="flex flex-wrap justify-center">
            <input
                type="text"
                class="mr-6 w-96 shadow-sm focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent mt-2 px-2 py-2 sm:text-sm border border-gray-300 rounded-lg"
                placeholder="Enter your message"
                v-model="text"
            />
            <button v-on:click="sendMessage" class="bg-indigo-500 hover:bg-blue-700 text-white text-sm px-6 rounded-full">
                Send
            </button>
        </div>
        <span v-if="errorText" class="text-red-500 text-xs" >{{errorText}}</span>
    </div>
</template>

<script>
import io from 'socket.io-client';
import axios from "axios";
import moment from 'moment';

var socket = io('http://localhost:3000');

export default {
  name: 'Chat',
  data() {
      return{
          chatMessages: [],
          text: "",
          username: "",
          errorName: "",
          errorText: "",
          sessionID: ""
      }
  },
  methods:{
      convertDate(date){
        return moment(date).format('MMMM Do YYYY, h:mm:ss a');
      },
      scrollToEnd () { 
           	this.$nextTick(() => {
            var content = this.$refs.refChat;
            content.scrollTop = content.scrollHeight
        }) 
      },
      sendMessage(){
          if(this.username.length < 6){
              this.errorName = "Please enter a username with minimum 6 characters"
              this.errorText = ""
          } else if(!this.text){
              this.errorText = "Please enter a message"
              this.errorName = ""
          } else {
              this.errorName = ""
              this.errorText = ""
              socket.emit('sendMessage', {
                username: this.username,
                text: this.text,
                sessionID: this.sessionID
              });
              this.text=""
              this.scrollToEnd();
          }
      }
  },
  created (){
      socket.on('connect', () => {
          this.sessionID = socket.id;
          console.log(this.sessionID);
      })
      socket.on('message', (data) => {
        this.chatMessages.push(data);
        console.log(this.chatMessages);
      })
      axios.get("http://localhost:3000/").then((result) => {
        this.chatMessages = result.data;
        console.log(this.chatMessages);
      })
  },
  mounted (){
      this.scrollToEnd();
  }
}
</script>

<style scoped lang="sass">

</style>
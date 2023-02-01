<template>
  <div>
    <ul class="list-group">
      <li v-for="chat in chats" :key="chat.id" class="list-group-item">
        {{ chat.source.name }} > {{ chat.message }}
      </li>
    </ul>
    <input type="text" v-model="message">
    <button @click="sendMessage()">kirim</button>
  </div>
</template>

<script>
export default {
  name: "chat-view",
  data() {
    return {
      userId: 0,
      name: "anonym",
      message: "",
      chats: [],
      randomString:
        "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789",
      roomId: "abcdef123456789"
    };
  },
  mounted() {
    function getRandomInt(max) {
      return Math.floor(Math.random() * max);
    }
    this.userId = getRandomInt(100);

    const name = prompt("What is your name?");

    this.name = name;

    const roomId = prompt("Room ID nya kakak !!")

    this.roomId = roomId;

    this.$socket.emit("joinRoom", this.roomId, this.name);

    this.loadDatabase();
  },
  sockets: {
    connect: function () {
      console.log('socket connected')
    },
    newUser: function () {
      console.log('socket and new user connected')
    },
    userConnected: function (username) {
      console.log('socket and new user connected', username)
    },
    chatMessage: function (data) {
      console.log('this method was fired by the socket server. eg: io.emit("customEmit", data)')
      console.log(data, this.userId);
      this.appendMessage(data);
      if(data.source.user_id != this.userId) {
        console.log('bagi link bokep tan')
      }
    },
  },
  methods: {
    saveDatabase: function() {
      const dbName = `chat_${this.roomId}`
      localStorage.setItem(dbName, JSON.stringify(this.chats));
    },
    loadDatabase: function() {
      const dbName = `chat_${this.roomId}`
      const chatStore = localStorage.getItem(dbName) || null;
      let chats = [];

      if(chatStore) {
        const parseChat = JSON.parse(chatStore);
        chats = [...parseChat];
      }

      this.chats = [...chats];
    },
    getRandomInt: function(max) {
      return Math.floor(Math.random() * max);
    },
    generateString: function (length) {
      let result = " ";
      const charactersLength = this.randomString.length;
      for (let i = 0; i < length; i++) {
        result += this.randomString.charAt(
          Math.floor(Math.random() * charactersLength)
        );
      }

      return result;
    },
    appendMessage: function (data) {

      this.chats.push(data);

      this.saveDatabase();
    },
    sendMessage: function () {
      console.log('pesan', this.message);

      const data = {
        id: this.generateString(30),
        message: this.message,
        source: {
          user_id: this.userId,
          name: this.name,
        },
      };

      this.$socket.emit("sendChatMessage", data);

      // this.appendMessage(data);

      this.message = "";
    },
  },
};
</script>

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
      userId: 1,
      name: "anonym",
      message: "",
      chats: [],
      randomString:
        "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789",
    };
  },
  created() {
    const name = prompt("What is your name?");

    this.name = name;
  },
  sockets: {
    connect: function () {
      console.log('socket connected')
    },
    newUser: function () {
      console.log('socket and new user connected')
    },
    chatMessage: function (data) {
      console.log('this method was fired by the socket server. eg: io.emit("customEmit", data)')
      console.log(data.message);
      this.appendMessage(data.message);
    }
  },
  methods: {
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
    },
    sendMessage: function () {
      console.log(this.message);

      const data = {
        id: this.generateString(30),
        message: this.message,
        source: {
          user_id: this.userId,
          name: this.name,
        },
      };

      this.$socket.emit("sendChatMessage", data);

      this.appendMessage(data);

      this.message = "";
    },
  },
};
</script>

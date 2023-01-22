<template>
  <div>
    <ul class="list-group">
      <li v-for="chat in chats" :key="chat.id" class="list-group-item">
        {{ chat.source.name }} > chat.message
      </li>
    </ul>
  </div>
</template>

<script>
  const name = 'anonym';

  const socket = io('http://localhost:3000')

  socket.emit('new-user', name)

  socket.on('chat-message', data => {
    appendMessage(`${data.name}: ${data.message}`)
  })

  socket.on('user-connected', name => {
    appendMessage(`${name} connected`)
  })

  socket.on('user-disconnected', name => {
    appendMessage(`${name} disconnected`)
  })

  export default {
    name: "chat-view",
    data() {
      return {
        userId: 1,
        name: "anonym",
        message: "",
        chats: [],
        randomString: "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789"
      }
    },
    created() {
      const name = prompt('What is your name?')

      this.name = name;
    },
    methods: {
      generateString: function(length) {
          let result = ' ';
          const charactersLength = characters.length;
          for ( let i = 0; i < length; i++ ) {
              result += characters.charAt(Math.floor(Math.random() * charactersLength));
          }

          return result;
      },
      appendMessage: function(message) {
        const data = {
          id: this.generateString(30),
          message,
          source: {
            user_id: this.userId,
            name: this.name
          }
        };

        this.chats.push(data);
      },
      sendMessage: function() {
        socket.emit("send-chat-message", this.message);
        
        this.appendMessage(this.message);

        this.message = "";
      }
    }
  }
</script>
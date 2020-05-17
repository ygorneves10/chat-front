<template>
  <div class="home">
    <form @submit.prevent="sendMessage">
      <input type="text" v-model="text" />
      <button type="submit">Enviar</button>
    </form>
    <p v-for="chat in messages" :key="chat.message">
      <strong>{{chat.user}}</strong>
      : {{chat.message}}
    </p>
  </div>
</template>

<script>
import io from "socket.io-client";

export default {
  name: "Home",
  data() {
    return {
      text: "",
      messages: [],
      socket: null,
      userId: null
    };
  },
  mounted() {
    this.socket = io("https://mighty-spire-83413.herokuapp.com");

    this.socket.on("connect", () => {
      this.userId = this.socket.id;
      this.messages.push({
        user: "Bot",
        message: `You're connected with id: ${this.userId}`
      });
    });
    this.socket.on("received", message => {
      this.messages.push(message);
    });
  },
  methods: {
    sendMessage() {
      this.socket.emit("message", {
        user: this.userId,
        message: this.text
      });
      this.text = "";
    }
  }
};
</script>

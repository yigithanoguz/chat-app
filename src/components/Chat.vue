<template>
  <div v-if="!username">
    <input v-model="tempUsername" placeholder="Enter your username" />
    <button @click="setUsername">Join Chat</button>
  </div>
  <div v-else>
    <div class="chat-box">
      <div v-for="(msg, index) in messages" :key="index">
        <strong>{{ msg.username }}:</strong> {{ msg.text }}
      </div>
    </div>
    <input
      v-model="message"
      placeholder="Type a message"
      @keyup.enter="sendMessage"
    />
  </div>
</template>

<script>
import { io } from "socket.io-client";

export default {
  data() {
    return {
      username: "", // Kullanıcı adı
      tempUsername: "unkown_user", // Geçici olarak kullanıcı adı
      message: "", // Kullanıcının yazdığı mesaj
      messages: [], // Mesajlar
      socket: null, // Socket.io bağlantısı
    };
  },
  methods: {
    setUsername() {
      if (this.tempUsername) {
        this.username = this.tempUsername;
        this.socket = io("http://192.168.246.1:3000"); // Backend'e bağlan
        this.socket.emit("set username", this.username); // Kullanıcı adı belirleme
        this.socket.on("chat message", (msg) => {
          this.messages.push(msg); // Mesajları al ve listeye ekle
        });
      }
    },
    sendMessage() {
      if (this.message.trim() !== "") {
        this.socket.emit("chat message", {
          username: this.username,
          text: this.message,
        });
        this.message = ""; // Mesajı gönderdiğinde temizle
      }
    },
  },
};
</script>

<style scoped>
.chat-box {
  max-height: 300px;
  overflow-y: auto;
  border: 1px solid #ccc;
  margin-bottom: 10px;
  padding: 10px;
}
input {
  padding: 5px;
  margin-top: 10px;
  width: 100%;
}
button {
  padding: 5px 10px;
}
</style>

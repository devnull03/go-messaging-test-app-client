<template>
  <main>
    <div class="input-wrapper">
      <input
        type="text"
        v-model="text"
        @input="onPress"
        id="message-box"
        @keypress.enter="onEnter"
        placeholder="Message"
      />
      <p>
        {{ typing ? `${userName} is typing . . .` : "&nbsp" }}
      </p>
    </div>
    <div class="message-list">
      <div class="message" v-for="message in messages">
        <p>{{ message.username }}</p>
        {{ message.content }}
      </div>
    </div>
  </main>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "Main",
  data() {
    interface Data {
      serverAddr: string
      text: string;
      messages: { username: string, content: string }[];
      userName: string;
      typing: boolean;
      connection: WebSocket | null;
    }
    return <Data>{
      serverAddr: "wss://go-messaging-test-app-server.arnav03mehta.repl.co/socket",
      text: "",
      messages: [],
      userName: "",
      typing: false,
      connection: null,
    };
  },
  methods: {
    onEnter() {
      if (!this.text) return;
      if (!this.connection) return;

      this.connection.send(
        JSON.stringify({ username: this.userName, content: this.text })
      );

      //   this.messages.push({ content: this.text });
      this.text = "";
      this.typing = false;
    },
    onPress() {
      //   this.typing = this.text.length > 0;
    },
    getUserName() {
      const userName = localStorage.getItem("username");
      if (userName) {
        this.userName = userName;
        return;
      }
      this.userName = `anon${Math.floor(Math.random() * 9999) + 1000}`;
      localStorage.setItem("username", this.userName);
    },
  },
  created() {
    this.getUserName();
    console.log("Starting connection to WebSocket Server");
    this.connection = new WebSocket(this.serverAddr);

    this.connection.onopen = (event) => {
      console.log(event);
      console.log("Successfully connected to the echo websocket server...");
    };
    this.connection.onmessage = (event) => {
      console.log(event);
      this.messages.push(JSON.parse(JSON.parse(event.data).body));
    };
  },
});
</script>

<style>
main {
  padding: 2rem;
  padding-bottom: 0.5rem;
  min-height: 100vh;
  display: flex;
  flex-direction: column-reverse;
  row-gap: 2rem;
}

.message-list {
  display: flex;
  flex-direction: column;
  font-size: large;
  row-gap: 0.65rem;
}
.message p {
  font-size: x-small;
  color: rgba(161, 161, 161, 0.64);
}
.input-wrapper {
  display: flex;
  flex-direction: column;
  margin: 0%;
  padding: 0%;
}
.input-wrapper p {
  margin-left: 0.75rem;
  /* font-size: small; */
}
input#message-box {
  width: 100%;
  font-size: medium;
  padding: 1rem;
  background-color: #313131;
  border: #313131;
  border-radius: 0.75rem;
  color: inherit;
}
input:focus-visible {
  outline: none;
}
</style>

<script>
export default {
  name: "App",
  data() {
    return {
      wssUrl: "wss://socketsbay.com/wss/v2/1/demo/",
      socket: null,

      messages: ["initial text. Not From Server."],
      inputText: "",
    };
  },
  created() {
    // call this method in created if you want to connect to Websocket on page load
    // this.socketInit();
  },
  beforeUnmount() {
    // call this method if you want to remove websocket before unmounting the Vue instance
    // like when closing the page, or navigating the page
    // this.socketClose();
  },
  methods: {
    socketInit() {
      console.info("Starting connection to WebSocket Server");
      const socket = new WebSocket(this.wssUrl);
      socket.onopen = this.onOpenCallback;
      socket.onmessage = this.onMessageCallback;
      socket.onerror = this.onErrorCallback;
      socket.onclose = this.onCloseCallback;
      this.socket = socket;
    },
    onOpenCallback(onOpenEvent) {
      // do something with new connection,
      // like sending hi to server when connection is open
      console.log(onOpenEvent);
      console.log("Successfully connected to the echo websocket server... 🚀");
    },
    onMessageCallback(serverMessage) {
      // do something when message is received from server
      // message type depends on server
      console.log(serverMessage);
      this.addMessageInList(serverMessage.data);
    },
    onErrorCallback(errorCause) {
      // do something when connection with WebSocket has been closed
      // maybe data couldn't be sent
      console.log(errorCause);
    },
    onCloseCallback(reason) {
      // do something when we gracefully close the WebSocket
      console.log(reason);
    },

    // there are only two instance methods: socket.send() and socket.close()
    /**
     * Send websocket server a message
     * @param {string} message the message to send. message must be of type String, ArrayBuffer, Blob, TypedArray, DataView
     */
    socketSend(message) {
      if (typeof message !== "string") {
        console.error("Incorrect Type of data for sending the message");
        return;
      }
      this.socket.send(message);
    },
    socketClose() {
      // change the code and reason parameter if required
      this.socket.close(1000, "closed the connection gracefully. ⌛");
      setTimeout(() => {
        this.deleteSocketInstance();
      }, 500);
    },

    /**
     * deletes the socket instance stored in data of the component
     */
    deleteSocketInstance() {
      this.socket = null;
      console.log("deleted the instance stored in Vue. 😀");
    },

    addMessageInList(message) {
      this.messages.push(message);
    },

    submitMessage() {
      const inputText = this.inputText;

      // if empty message return
      if (!inputText) return;

      this.socketSend(inputText);

      // I am calling add message to include the input text in the list
      // it can be removed if server designed to send messages back to client
      this.addMessageInList(inputText);

      // clearing input text after submitted
      this.inputText = "";
    },
  },
};
</script>

<template>
  <div>
    <h3>Websocket is {{ socket ? "Open" : "Closed" }}</h3>
    <hr />
    <div class="button-container">
      <button v-if="!socket" class="btn" @click="socketInit">
        Open Websocket
      </button>
      <button v-else class="btn btn-close" @click="socketClose">
        Close Websocket
      </button>
    </div>
    <hr />

    <div v-if="socket" class="websocket-message-container">
      <div class="form-container">
        <form class="form__group" @submit.prevent="submitMessage">
          <input
            v-model="inputText"
            :autocomplete="false"
            :autocorrect="false"
            type="text"
            class="form__input"
            id="name"
            placeholder="Enter a meesage to send"
          />
          <label for="name" class="form__label"
            >Press Enter to send the message</label
          >
        </form>
      </div>
      <div class="message-container">
        <ul>
          <li v-for="(message, index) in messages" :key="index">
            {{ message }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* input starts here */
.form__label {
  font-size: 1.2rem;
  margin-left: 2rem;
  display: block;
  transition: all 0.3s;
  transform: translateY(0rem);
}

.form__input {
  font-family: "Roboto", sans-serif;
  color: #333;
  font-size: 1.2rem;
  margin: 0 auto;
  padding: 1rem;
  border-radius: 0.2rem;
  background-color: rgb(255, 255, 255);
  border: none;
  width: 90%;
  display: block;
  border-bottom: 0.3rem solid rgb(13, 118, 239);
  transition: all 0.3s;
}

.form__input:placeholder-shown + .form__label {
  opacity: 0;
  visibility: hidden;
  -webkit-transform: translateY(-4rem);
  transform: translateY(-4rem);
}
/* input ends here */

/* button style starts here */
.btn {
  height: 3em;
  padding: 0.75em;
  will-change: filter;
  transition: filter 300ms;
  margin-left: 12px;
}
.btn:hover {
  filter: drop-shadow(0 0 1em #646cffaa);
}
.btn:hover {
  filter: drop-shadow(0 0 1em #42b883aa);
}

.btn-close {
  background-color: #d22f2f;
}
.btn-close:hover {
  filter: drop-shadow(0 0 1em #b04f4f);
}
/* button style ends here */

/* list style starts here */
ul {
  padding: 0;
  margin: 0;
  max-width: 700px;
  position: relative;
}

ul::before {
  content: "";
  width: 0.5rem;
  height: 100%;
  position: absolute;
  top: 0;
  left: 8%;
  background: peachpuff;
  z-index: -1;
}

li {
  padding: 1rem;
  border-radius: 1.5rem;
  background: rgb(13, 118, 239);
  display: flex;
  justify-content: center;
}

li + li {
  margin-top: 1rem;
}

::marker {
  font-weight: 600;
  color: tomato;
  font-size: 1.8rem;
}
/* list style ends here */
</style>

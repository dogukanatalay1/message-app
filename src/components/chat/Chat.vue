<template>
  <div class="container">
    <div class="row clearfix">
      <div class="col-lg-12">
        <div class="card chat-app">
          <div id="plist" class="people-list">
            <div class="input-group">
              <input type="text" class="form-control" v-model="name" />
              <button @click="setName" class="btn btn">Join</button>
            </div>
            <div>Online: {{ online.length }}</div>
            <b-alert show variant="success" v-for="inf in info" :key="inf">
              {{ inf.name }} {{ inf.type }}
            </b-alert>

            <!-- LEFT CHATS LIST -->
            <ul class="list-unstyled chat-list mt-2 mb-0">
              <li class="clearfix">
                <img src="@/assets/user.jpg" alt="avatar" />
                <div class="about">
                  <div class="name">Doğukan Atalay</div>
                </div>
              </li>
              <button @click="deleteAllMessages" class="btn btn-danger">
                Delete all messages
              </button>
            </ul>
            <!-- * * * * -->
          </div>
          <div class="chat">
            <div class="chat-header clearfix">
              <div class="row">
                <div class="col-lg-6">
                  <a
                    href="javascript:void(0);"
                    data-toggle="modal"
                    data-target="#view_info"
                  >
                    <img src="@/assets/user.jpg" alt="avatar" />
                  </a>
                  <div class="chat-about">
                    <h6 class="m-b-0">Lionel Messi</h6>
                    <small v-if="typing">{{ typing }} typing...</small>
                  </div>
                </div>
              </div>
            </div>
            <div class="chat-history">
              <ul class="m-b-0">
                <!-- MESSAGES -->
                <li
                  class="clearfix"
                  v-for="message in messages"
                  :key="message.type"
                >
                  <Message :message="message" />
                </li>
              </ul>
            </div>
            <div class="chat-message clearfix">
              <form @submit.prevent="send">
                <div class="input-group mb-0">
                  <input
                    type="text"
                    class="form-control"
                    placeholder="Enter text here..."
                    v-model="newmessage"
                  />
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import io from "socket.io-client";
import Message from "../../components/chat/message/Message.vue";
import axios from "axios";

export default {
  name: "ChatComponent",
  components: {
    Message,
  },
  data() {
    return {
      newmessage: null,
      messages: [],
      typing: false,
      online: [],
      name: null,
      info: [],
      connectionCount: 0,
    };
  },
  methods: {
    send() {
      this.socketInstance.emit("chat-message", {
        message: this.newmessage,
        user: this.name,
      });
      this.messages.push({ message: this.newmessage, type: 0, by: "Me" });
      this.newmessage = null;
    },
    isTyping() {
      this.this.socketInstance.emit("typing", this.name);
    },
    setName() {
      this.socketInstance.emit("joined", this.name);
      this.online.push(name);
    },
    getPastMessages() {
      axios.get(`${process.env.VUE_APP_BASE_URL}/chat`).then((res) => {
        this.messages = res.data.data;
      });
    },
    deleteAllMessages() {
      axios
        .delete(`${process.env.VUE_APP_BASE_URL}/chat/deleteAll`)
        .then((res) => {
          console.log(res);
        });
    },
  },
  mounted() {
    window.onbeforeunload = () => {
      this.socketInstance.emit("leaved", this.name);
    };
    this.socketInstance.on("noOfConnections", (count) => {
      this.connectionCount = count;
    });
  },
  watch: {
    newmessage(value) {
      value
        ? this.socketInstance.emit("typing", this.name)
        : this.socketInstance.emit("stoptyping");
    },
  },
  created() {
    this.socketInstance = io(process.env.VUE_APP_BASE_URL);

    this.getPastMessages();
    this.socketInstance.on("chat-message", (data) => {
      this.messages.push({ message: data.message, type: 1, by: data.user });
      this.typing = false;
    });

    this.socketInstance.on("typing", (data) => {
      this.typing = data;
    });
    this.socketInstance.on("stoptyping", () => {
      this.typing = false;
    });

    this.socketInstance.on("leaved", (name) => {
      this.online.splice(this.online.indexOf(name));
      this.info.push({ name: name, type: "Leaved" });
      setTimeout(() => {
        this.info = [];
      }, 4000);
    });

    this.socketInstance.on("joined", (name) => {
      this.online.push(name);
      this.info.push({ name: name, type: "Joined" });
      setTimeout(() => {
        this.info = [];
      }, 4000);
    });
  },
};
</script>

<style lang="scss" scoped>
@import "@/assets/main.scss";
</style>

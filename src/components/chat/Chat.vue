<template>
  <div>
    <div class="container" id="app">
      <div class="col-md-4 col-lg-6 offset-lg-3 offset-md-4">
        <div v-if="ready">
          <h4>{{ name }}</h4>
        </div>
        <form @submit.prevent="setName" class="mt-4" v-else>
          <div class="form-group row">
            <label>Set Your Name First</label>
            <input
              type="text"
              class="form-control col-9"
              v-model="name"
              placeholder="Type Here"
            />
            <input
              type="submit"
              value="Add"
              class="btn btn-sm btn-info ml-1 col-2"
            />
          </div>
        </form>
        <div class="card bg-info" v-if="ready">
          <div class="card-header text-white">
            Sautomaid Chatroom
            <span class="float-right"
              >{{ connectionCount }} connections,
              {{ online.length }} Online</span
            >
          </div>

          <ul class="list-group list-group-flush text-right">
            <small v-if="typing" class="text-white">
              <i>{{ typing }} is typing...</i>
            </small>
            <li
              class="list-group-item d-flex flex-row"
              v-for="message in messages"
              :key="message.type"
            >
              <Message :message="message" />
            </li>
          </ul>

          <div class="card-body">
            <form @submit.prevent="send">
              <div class="form-group">
                <input
                  type="text"
                  class="form-control"
                  v-model="newmessage"
                  placeholder="Type Here"
                />
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import io from "socket.io-client";
import Message from "../../components/chat/message/Message.vue";

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
      ready: false,
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
      this.ready = true;
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
      }, 5000);
    });

    this.socketInstance.on("joined", (name) => {
      this.online.push(name);
      this.info.push({ name: name, type: "Joined" });
      setTimeout(() => {
        this.info = [];
      }, 5000);
    });
  },
};
</script>

<style lang="scss" scoped></style>

<template>
  <div
    :class="[
      { 'my-message': !ownMessage },
      { 'other-message': ownMessage },
      message,
    ]"
  >
    <div class="d-flex flex-row">
      <span class="message__user" v-if="message.user">{{ message.user }} </span>
      <span v-if="!ownMessage" class="message__user"> {{ message.by }} : </span>
      <span class="message__text" v-if="message.text">{{ message.text }}</span>
      <div class="message__text">
        {{ message.message }}
      </div>
    </div>
  </div>  
</template>

<script>
export default {
  name: "MessageBox",
  data() {
    return {
      ownMessage: this.message.type === 0,
    };
  },
  props: {
    message: {
      type: Object,
      default: null,
    },
  },
};
</script>

<style lang="scss" scoped>
@import "@/assets/variables.scss";

.message {
  color: $color_3;
  padding: 18px 20px;
  line-height: 26px;
  font-size: 16px;
  border-radius: 7px;
  display: inline-block;
  position: relative;
  &:after {
    bottom: 100%;
    left: 7%;
    border: solid transparent;
    content: " ";
    height: 0;
    width: 0;
    position: absolute;
    pointer-events: none;
    border-bottom-color: $border-bottom-color_1;
    border-width: 10px;
    margin-left: -10px;
  }
}
.my-message {
  background: #efefef;
  &:after {
    bottom: 100%;
    left: 30px;
    border: solid transparent;
    content: " ";
    height: 0;
    width: 0;
    position: absolute;
    pointer-events: none;
    border-bottom-color: $border-bottom-color_2;
    border-width: 10px;
    margin-left: -10px;
  }
}
.other-message {
  background: #e8f1f3;
  text-align: right;
  float: right;
  &:after {
    border-bottom-color: $border-bottom-color_3;
    left: 93%;
  }
}

.chat-message {
  padding: 20px;
}
</style>

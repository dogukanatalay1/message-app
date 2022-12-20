<template>
  <div class="message justify-self-end">
    <span class="message__user"> {{ message.by }} : </span>
    <div class="message__text">{{ message.message }}</div>
  </div>
</template>

<script>
export default {
  name: "MessageBox",
  data() {
    return {};
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
.message {
  display: flex;
  box-shadow: 1px 1px 1px 1px darkgray;
  padding: 5px;
  box-sizing: content-box;
  border-radius: 10px;
  width: fit-content;
  height: fit-content;
}

.alignSelfEnd {
  align-self: flex-end;
}
</style>

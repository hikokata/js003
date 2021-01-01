<template>
  <div class="chat-form">
    <div class="form-container">
      <input type="text" class="message" v-model="message" />
      <button class="submit" @click="submit">送信</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    applyEvent: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      message: ""
    };
  },
  methods: {
    submit() {
      this.$emit(this.applyEvent, this.message);
      this.message = "";
    }
  }
};
</script>

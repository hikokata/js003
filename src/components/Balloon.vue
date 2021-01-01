<template>
  <div class="conversation-balloon" :class="speaker">
    <div class="avatar">
      <div v-if="speaker === 'my'">
        <img src="../assets/10012smilingfacewithsunglasses_110016.png" />
      </div>
      <div v-else>
        <img src="../assets/10020smilingface_109951.png" />
      </div>
      <p class="name">{{ name }}</p>
    </div>
    <p class="message">{{ message }}</p>
  </div>
</template>

<script>
export default {
  props: {
    name: {
      type: String,
      required: true
    },
    speaker: {
      type: String,
      required: true,
      validator: value => {
        return ["my", "other"].includes(value);
      }
    },
    message: {
      type: String,
      required: true
    }
  }
};
</script>

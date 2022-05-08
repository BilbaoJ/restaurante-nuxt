<template>
  <div>
    <b-modal ref="my-modal" id="my-modal" title="BootstrapVue">
      <p class="my-4">{{ message }}</p>
      <b-button class="mt-3" variant="outline-danger" block @click="hideModal"
        >Close Me</b-button
      >
    </b-modal>
  </div>
</template>
<script>
export default {
  props: {
    message: String,
  },
  watch: {
    message(newMessage) {
      if (newMessage.length > 0) {
        this.$refs["my-modal"].show();
      }
    },
  },
  methods: {
    hideModal() {
      this.$refs["my-modal"].hide();
    },
  },
};
</script>
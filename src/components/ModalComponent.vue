<template>
  <div class="modal" :class="overlap">
    <div class="head" :class="headColorModal">
      <p>{{ title }}</p>
      <img
        @click="close"
        src="@/assets/close-icon.svg"
        alt="Close"
        title="Close"
      />
    </div>
    <div class="body" :class="bodyColorModal">
      <slot></slot>
    </div>
  </div>
</template>

<script>
export default {
  computed: {
    headColorModal() {
      return this.headColor ? this.headColor : null;
    },
    bodyColorModal() {
      return this.bodyColor ? this.bodyColor : null;
    },
    overlap() {
      return this.overlapping ? this.overlapping : null;
    },
  },
  props: {
    title: {
      type: String,
      default: "title",
    },
    headColor: {
      type: String,
      default: null,
    },
    bodyColor: {
      type: String,
      default: null,
    },
    overlapping: {
      type: String,
      default: null,
    },
  },
  methods: {
    close() {
      this.$emit("close");
    },
  },
};
</script>

<style scoped>
.modal {
  z-index: 2;
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  background-color: white;
  overflow: scroll;
}

.head {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  padding: 24px 24px;
  box-sizing: border-box;
  min-height: 10%;
}

.body {
  min-height: 90%;
}

p {
  margin: 0;
  font-size: 1.5rem;
  color: var(--brand-yellow);
}

.text-white p {
  color: white !important;
}

.dark-blue {
  background: #121f3d !important;
}

.text-yellow {
  color: var(--brand-gold) !important;
}

.overlap {
  z-index: 5;
}
</style>

<template>
  <header>
    <img @click="reload" src="@/assets/logo.png" alt="Logo" />
    <h1><span>Care</span>Fines</h1>
    <span></span>
    <img
      class="search-icon"
      src="@/assets/search-icon.svg"
      alt="search-icon"
      title="search-icon"
      @click="switchShowSearch"
    />
  </header>
</template>

<script>
export default {
  methods: {
    reload() {
      location.reload();
    },
    switchShowSearch() {
      this.$emit("showSearch");
    },
  },
};
</script>

<style scoped>
header {
  display: flex;
  justify-content: space-around;
  align-items: center;
  width: 100vw;
}

img,
span {
  width: 48px;
}

h1 {
  font-size: 1.4rem;
  color: var(--brand-yellow);
  font-family: LilitaOne, serif;
  letter-spacing: 0.1rem;
}

h1 span {
  color: var(--brand-gold);
}

.search-icon {
  position: absolute;
  right: 1.5rem;
  cursor: pointer;
}
</style>

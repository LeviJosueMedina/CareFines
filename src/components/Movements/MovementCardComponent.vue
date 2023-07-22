<template>
  <div class="movement">
    <div @click="openDetail" class="content">
      <h4>{{ title }}</h4>
      <p>{{ description }}</p>
    </div>
    <div class="action">
      <div>
        <img
          src="@/assets/edit-icon.svg"
          alt="Edit"
          title="Edit"
          class="action-image"
          @click="edit"
        />
        <img
          src="@/assets/delete-icon.svg"
          alt="Delete"
          title="Delete"
          class="action-image margin-5p"
          @click="remove"
        />
      </div>
      <p
        :class="{
          red: isNegative,
          green: !isNegative,
        }"
      >
        {{ amountCurrency }}
      </p>
    </div>
  </div>
</template>

<script setup>
import { toRefs, defineProps, defineEmits, computed } from "vue";

const currencyFormatter = new Intl.NumberFormat("es-MX", {
  style: "currency",
  currency: "MXN",
});

const props = defineProps({
  id: {
    type: Number,
  },
  title: {
    type: String,
  },
  description: {
    type: String,
  },
  amount: {
    type: Number,
  },
});

const { id, title, description, amount } = toRefs(props);

const amountCurrency = computed(() => currencyFormatter.format(amount.value));

const isNegative = computed(() => amount.value < 0);

const emit = defineEmits(["remove", "edit", "openDetail"]);

const remove = () => {
  emit("remove", id.value);
};

const edit = () => {
  emit("edit", id.value);
};

const openDetail = () => {
  emit("openDetail");
};
</script>

<style scoped>
.movement {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  padding: 16px;
  background-color: #e6f9ff;
  border-radius: 8px;
  box-sizing: border-box;
}

.movement .content {
  width: 70%;
  overflow: hidden;
}

.movement .action {
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  flex-direction: column;
}

h4,
p {
  margin: 0;
  padding: 0;
}

p {
  font-size: 0.9rem;
}

h4 {
  margin-bottom: 8px;
}

.movement .action img {
  margin-bottom: 16px;
}

.red {
  color: red;
}

.green {
  color: green;
}
.action-image {
  height: 15px;
  width: 15px;
}
.margin-5p {
  margin-left: 7px;
}
</style>

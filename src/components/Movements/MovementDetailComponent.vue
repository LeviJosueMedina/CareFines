<template>
  <div class="detail">
    <div class="content">
      <div class="field">
        <div class="top-area">
          <h4 class="sub-title">{{ $t("labels.title") }}</h4>
          <div class="action">
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
        </div>
        <div class="text">
          {{ movement.title }}
        </div>
      </div>
      <div class="field">
        <h4 class="sub-title">{{ $t("labels.category") }}</h4>
        <div class="text">
          {{ movement.category }}
        </div>
      </div>
      <div class="field">
        <h4 class="sub-title">{{ $t("labels.amount") }}</h4>
        <div class="text" :class="movement.type">
          {{ movement.amount }}
        </div>
      </div>
      <div class="field">
        <h4 class="sub-title">{{ $t("labels.description") }}</h4>
        <div class="text">
          {{ movement.description }}
        </div>
      </div>
      <div class="field">
        <h4 class="sub-title">{{ $t("labels.date") }}</h4>
        <div class="text">{{ movement.time }}</div>
      </div>
    </div>
  </div>
</template>

<script>
const currencyFormatter = new Intl.NumberFormat("es-MX", {
  style: "currency",
  currency: "MXN",
});
export default {
  data() {
    return {
      movement: {
        id: null,
        title: null,
        date: null,
        type: null,
        Description: null,
        category: null,
      },
      categories: [
        { id: 1, name: this.$t("categories.services") },
        { id: 2, name: this.$t("categories.rent") },
        { id: 3, name: this.$t("categories.transport") },
        { id: 4, name: this.$t("categories.healthcare") },
        { id: 5, name: this.$t("categories.cleaningSupplies") },
        { id: 6, name: this.$t("categories.taxes") },
        { id: 7, name: this.$t("categories.food") },
        { id: 8, name: this.$t("categories.clothing") },
        { id: 9, name: this.$t("categories.salary") },
        { id: 10, name: this.$t("categories.personalCare") },
        { id: 11, name: this.$t("categories.entertainment") },
        { id: 12, name: this.$t("categories.others") },
      ],
    };
  },
  props: {
    movementDetailed: {
      type: Object,
      default: () => ({}),
    },
  },
  created() {
    let movementDetailed = this.movementDetailed;
    if (movementDetailed) {
      this.movement.id = movementDetailed.id;
      this.movement.title = movementDetailed.title;
      this.movement.amount = currencyFormatter.format(movementDetailed.amount);
      this.movement.time = movementDetailed.time
        ? this.formatDate(movementDetailed.time)
        : "";
      this.movement.type = this.amountType(movementDetailed.amount);
      this.movement.description = movementDetailed.description
        ? movementDetailed.description
        : this.$t("blankData.description");
      this.categories.filter((c) => {
        if (c.id === movementDetailed.category) {
          this.movement.category = c.name;
        }
      });
    }
  },
  methods: {
    formatDate(date) {
      let formatDate = new Date(date);
      return (
        formatDate.getMonth() +
        1 +
        "/" +
        formatDate.getDate() +
        "/" +
        formatDate.getFullYear()
      );
    },
    amountType(amount) {
      let type = "invalid";
      if (amount > 0) {
        type = "income";
      }
      if (amount < 0) {
        type = "expense";
      }
      return type;
    },
    remove() {
      this.$emit("remove", this.movement.id);
    },
    edit() {
      this.$emit("edit", this.movement.id);
    },
  },
};
</script>

<style scoped>
.detail {
  padding: 1.5rem;
  overflow: hidden;
}
.detail .content {
  font-size: 1.24rem;
  border: 1px solid #ffc300;
  border-radius: 15px;
  padding: 2rem 1.5rem 1.5rem 1.5rem;
  min-height: 41rem;
  overflow-y: scroll;
}
.sub-title {
  font-size: 1.2rem;
  width: 75%;
  margin: 0;
}
.text {
  font-size: 1rem;
  padding: 1rem 0 2.5rem 0;
  word-break: break-all;
}

.income {
  color: green;
}
.expense {
  color: red;
}
.invalid {
  color: lightslategrey;
}
.field {
  display: flex;
  flex-direction: column;
}
.action {
  display: flex;
  justify-content: flex-end;
  width: 25%;
}
.action-image {
  margin-left: 15px;
  height: 22px;
  width: 22px;
}

.top-area {
  display: flex;
}
</style>

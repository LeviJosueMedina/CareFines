<template>
  <button @click="showModal = true">{{ $t("buttons.newMovement") }}</button>
  <teleport to="#app">
    <Modal
      v-show="showModal"
      :title="title"
      :overlapping="'overlap'"
      @close="close"
    >
      <form @submit.prevent="submit">
        <div class="field">
          <label>{{ $t("labels.category") }} *</label>
          <select v-model="properties.category.id">
            <option
              v-for="category in categories"
              :key="category.id"
              :value="category.id"
            >
              {{ category.name }}
            </option>
          </select>
        </div>
        <div class="field">
          <label>{{ $t("labels.date") }} </label>
          <input type="date" v-model="properties.time" />
        </div>
        <div class="field">
          <label>{{ $t("labels.title") }} *</label>
          <input type="text" v-model="properties.title" />
        </div>
        <div class="field">
          <label>{{ $t("labels.amount") }} *</label>
          <input type="number" v-model="properties.amount" />
        </div>
        <div class="field">
          <label>{{ $t("labels.description") }}</label>
          <textarea rows="4" v-model="properties.description"></textarea>
        </div>
        <div class="field">
          <label class="radio-label">
            <input
              type="radio"
              v-model="properties.movementType"
              value="Income"
            />
            <span>{{ $t("labels.income") }}</span>
          </label>
          <label class="radio-label">
            <input
              type="radio"
              v-model="properties.movementType"
              value="Expense"
            />
            <span>{{ $t("labels.expense") }}</span>
          </label>
        </div>
        <div class="action">
          <p v-if="submitDisabled" class="fieldsEmpty">
            {{ $t("blankData.emptyFields") }}
          </p>
          <button
            :class="{ buttonDisabled: submitDisabled }"
            :disabled="submitDisabled"
          >
            {{ actionType }}
          </button>
        </div>
      </form>
    </Modal>
  </teleport>
</template>

<script>
import Modal from "./ModalComponent.vue";
export default {
  inheritAttrs: false, // Extraneous non-props attributes were passed to component but could not be automatically inherited
  components: {
    Modal,
  },
  props: {
    editedItem: {
      type: Object,
      default: () => ({}),
    },
    movements: {
      type: Object,
      default: () => ({}),
    },
  },
  data() {
    return {
      properties: {
        id: null,
        title: "",
        amount: 0,
        description: "",
        movementType: "Expense",
        time: new Date().toISOString().split("T")[0],
        category: {
          id: null,
        },
      },
      selectedCategory: "",
      showModal: false,
      submitDisabled: true,
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
  computed: {
    actionType() {
      return this.properties.id
        ? this.$t("buttons.saveChanges")
        : this.$t("buttons.newMovement");
    },
    title() {
      return this.properties.id
        ? this.$t("labels.editMovement")
        : this.$t("labels.newMovement");
    },
  },
  created() {
    this.categoriesByAlphabeticalOrder();
  },
  watch: {
    editedItem: function (newVal) {
      if (newVal.id) {
        this.properties.id = newVal.id;
        this.properties.title = newVal.title;
        this.properties.amount =
          newVal.amount > 0 ? newVal.amount : -1 * newVal.amount;
        this.properties.description = newVal.description;
        this.properties.time = new Date(newVal.time)
          .toISOString()
          .split("T")[0]
          .toLocaleString();
        this.properties.movementType =
          newVal.amount && newVal.amount > 0 ? "Income" : "Expense";
        this.showModal = true;
        this.categories.filter((m) => {
          if (m.id === newVal.category) {
            this.properties.category.id = m.id;
          }
        });
      }
    },
    properties: {
      handler() {
        this.validForm();
      },
      deep: true,
    },
  },
  methods: {
    categoriesByAlphabeticalOrder() {
      this.categories = this.categories.sort(function (a, b) {
        // The options are returned alphabetically Ordered
        return a.name.localeCompare(b.name, "en", { numeric: true });
      });
    },
    validForm() {
      this.submitDisabled = !(
        this.properties.amount &&
        this.properties.amount > 0 &&
        this.properties.title &&
        this.properties.title.length > 0 &&
        this.properties.category.id
      );
    },
    submit() {
      if (this.properties && this.properties.id) {
        this.properties.amount =
          this.properties.movementType === "Income"
            ? this.properties.amount
            : -this.properties.amount;
        this.$emit("saveEdit", this.properties);
      } else {
        this.$emit("create", {
          title: this.properties.title,
          description: this.properties.description,
          amount:
            this.properties.movementType === "Income"
              ? this.properties.amount
              : -this.properties.amount,
          id: this.createId(),
          time: this.properties.time,
          category: this.properties.category.id,
        });
      }
      this.showModal = !this.showModal;
      this.setDefault();
      this.$emit("setDefault");
    },
    setDefault() {
      this.properties.id = "";
      this.properties.title = "";
      this.properties.description = "";
      this.properties.amount = 0;
      this.properties.movementType = "Income";
      this.properties.time = new Date().toISOString().split("T")[0];
      this.properties.category = {
        id: null,
      };
    },
    createId() {
      const ids = this.movements.map((object) => {
        return object.id;
      });

      const max = ids.length > 0 ? Math.max(...ids) : 0;

      return max ? max + 1 : 1;
    },
    close() {
      this.setDefault();
      this.$emit("setDefault");
      this.showModal = false;
    },
  },
};
</script>

<style scoped>
button {
  color: white;
  font-size: 1.25rem;
  border: none;
  width: 100%;
  padding: 24px 60px;
  border-radius: 60px;
  box-sizing: border-box;
  background-color: var(--brand-yellow);
  margin-bottom: 2rem;
}

form {
  font-size: 1.24rem;
  width: 100%;
}

form .action {
  padding: 0 24px;
  margin-top: 1.5rem;
}

.fieldsEmpty {
  font-size: 0.9rem;
  float: right;
}

.field {
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  padding: 16px 24px;
}

label {
  margin-bottom: 8px;
}

input,
textarea,
select {
  font-size: 1.24rem;
  border: 2px solid var(--brand-yellow);
  border-radius: 8px;
  padding: 8px;
}

input[type="number"] {
  text-align: right;
}

.radio-label {
  display: flex;
  align-items: center;
  margin-top: 8px;
}

.radio-label span {
  margin-top: 4px;
  margin-left: 8px;
}

input[type="radio"] {
  appearance: none;
  width: 1.24rem;
  height: 1.24rem;
  color: var(--brand-yellow);
  border: 2px solid var(--brand-yellow);
  border-radius: 50%;
}

input[type="radio"]:checked {
  background-color: var(--brand-yellow);
}

.buttonDisabled {
  opacity: 0.5;
}
</style>

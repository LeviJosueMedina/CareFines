<template>
  <div>
    <form @submit.prevent="search">
      <div class="field">
        <label>{{ $t("labels.category") }}</label>
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
        <label>{{ $t("labels.title") }}</label>
        <input type="text" v-model="properties.title" />
      </div>
      <div class="field">
        <label>{{ $t("labels.from") }}</label>
        <input type="date" v-model="properties.time.from" />
      </div>
      <div class="field">
        <label>{{ $t("labels.to") }}</label>
        <input type="date" v-model="properties.time.to" />
      </div>
      <div class="field dual">
        <div class="segment">
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
        <div class="segment center">
          <button>{{ $t("labels.search") }}</button>
        </div>
      </div>
    </form>
    <div class="field result-area" v-if="search">
      <div
        v-show="haveSearch && searchResult.length === 0"
        class="noCoincidences"
      >
        <h4>{{ $t("blankData.noCoincidences") }}</h4>
        <img
          src="@/assets/no-results-found.png"
          alt="not found"
          title="not found"
          style="height: 5rem; width: 5rem"
        />
      </div>

      <movement-card-component
        v-for="movement in searchResult"
        :key="movement.id"
        :id="movement.id"
        :title="validTextLength(movement.title)"
        :description="validTextDescription(movement.description, movement.time)"
        :amount="movement.amount"
        @remove="remove(movement.id)"
        @edit="edit(movement.id)"
        @openDetail="openDetail(movement)"
      />
    </div>

    <teleport v-if="showDetail" to="#app">
      <Modal
        v-show="showDetail"
        :title="$t('titles.movementDetail')"
        :head-color="'dark-blue text-white'"
        @close="closeDetail"
      >
        <movement-detail-component
          :movementDetailed="movementDetailed"
          @remove="remove"
          @edit="edit"
        />
      </Modal>
    </teleport>
  </div>
</template>

<script>
import MovementCardComponent from "@/components/Movements/MovementCardComponent.vue";
import Modal from "@/components/ModalComponent.vue";
import MovementDetailComponent from "@/components/Movements/MovementDetailComponent.vue";

export default {
  components: { MovementDetailComponent, Modal, MovementCardComponent },
  props: {
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
        movementType: "Income",
        time: {
          from: null,
          to: null,
        },
        category: {
          id: 0,
        },
      },
      movementDetailed: null,
      searchResult: [],
      showDetail: false,
      showSearch: false,
      haveSearch: false,
      categories: [
        { id: 0, name: this.$t("categories.all") },
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
  created() {
    this.categoriesByAlphabeticalOrder();
  },
  methods: {
    search() {
      let params = this.properties;
      let searchResult = [];

      searchResult = this.movements
        .filter((m) => {
          if (
            m.title
              .toLowerCase()
              .trim()
              .includes(params.title.toLowerCase().trim())
          ) {
            return m;
          }
        })
        .filter((m) => {
          if (params.movementType === "Income" && m.amount > 0) {
            return m;
          }
          if (params.movementType === "Expense" && m.amount < 0) {
            return m;
          }
        })
        .filter((m) => {
          if (params.category.id === m.category || params.category.id === 0) {
            return m;
          }
        })
        .filter((m) => {
          let time = new Date(m.time)
            .toISOString()
            .split("T")[0]
            .toLocaleString();

          if (params.time.to && !params.time.from) {
            if (time <= params.time.to) {
              return m;
            }
          }
          if (params.time.from && !params.time.to) {
            if (time >= params.time.from) {
              return m;
            }
          }
          if (time >= params.time.from && time <= params.time.to) {
            return m;
          }
          if (!params.time.from && !params.time.to) {
            return m;
          }
        });

      this.haveSearch = true;
      this.searchResult = searchResult;
    },
    validTextLength(val) {
      let textTitle = val;
      if (val.length > 20) {
        textTitle = val.substr(0, 20) + "...";
      }
      return textTitle;
    },
    validTextDescription(description, time) {
      let textDescription = description;
      if (textDescription.length > 20) {
        textDescription = this.validTextLength(textDescription);
      }
      if (textDescription.length === 0) {
        textDescription = this.formatDate(time);
      }

      return textDescription;
    },
    formatDate(date) {
      let newDate = new Date(date);
      return (
        newDate.getMonth() +
        1 +
        "/" +
        newDate.getDate() +
        "/" +
        newDate.getFullYear()
      );
    },
    categoriesByAlphabeticalOrder() {
      this.categories = this.categories.sort(function (a, b) {
        // The options are returned alphabetically Ordered
        return a.name.localeCompare(b.name, "en", { numeric: true });
      });
    },
    openDetail(movement) {
      this.movementDetailed = movement;
      this.showDetail = true;
    },
    edit(id) {
      this.closeDetail();
      this.close();
      this.$emit("edit", id);
    },
    remove(id) {
      this.closeDetail();
      this.close();
      this.$emit("remove", id);
    },
    closeDetail() {
      this.showDetail = false;
    },
    close() {
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
  padding: 15px 30px;
  border-radius: 60px;
  box-sizing: border-box;
  background-color: var(--brand-yellow);
  margin-bottom: 2rem;
}

form {
  font-size: 1.24rem;
  width: 100%;
  color: white;
}

.field {
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  padding: 16px 24px;
}

.result-area {
  max-height: 81vh;
  display: flex;
  flex-direction: column;
  gap: 8px;
  overflow-y: scroll;
}

.noCoincidences {
  color: white;
  text-align: center;
}

.dual {
  flex-direction: inherit;
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

.center {
  display: flex;
  justify-content: center;
}

.segment {
  width: 50%;
}
</style>

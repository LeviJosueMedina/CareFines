<template>
  <div class="movements">
    <div class="movements-header">
      <h2 class="title">{{ $t("titles.history") }}</h2>
      <h2 class="title option" @click="openSummary">
        {{ $t("titles.summary") }}
      </h2>
    </div>
    <div class="content">
      <teleport v-if="showDetail" to="#app">
        <Modal
          v-show="showDetail"
          :title="$t('titles.movementDetail')"
          :head-color="'dark-blue text-white'"
          @close="close"
        >
          <movement-detail-component
            :movementDetailed="movementDetailed"
            @remove="remove"
            @edit="edit"
          />
        </Modal>
      </teleport>

      <teleport v-if="showSummary" to="#app">
        <Modal
          v-show="showSummary"
          :title="$t('titles.movementsSummary')"
          :head-color="'dark-blue text-yellow'"
          :body-color="'dark-blue text-white'"
          @close="close"
        >
          <movements-summary-component :movements="movements" />
        </Modal>
      </teleport>

      <movement-card-component
        v-for="movement in movements"
        :key="movement.id"
        :id="movement.id"
        :title="validTextLength(movement.title)"
        :description="validTextDescription(movement.description, movement.time)"
        :amount="movement.amount"
        :time="movement.time"
        @remove="remove(movement.id)"
        @edit="edit(movement.id)"
        @openDetail="openDetail(movement)"
      />

      <div class="default-content" v-show="movements.length === 0">
        <h4 style="color: black">{{ $t("blankData.noMovements") }}</h4>
      </div>
    </div>
  </div>
</template>

<script>
import MovementCardComponent from "@/components/Movements/MovementCardComponent.vue";
import MovementDetailComponent from "@/components/Movements/MovementDetailComponent.vue";
import Modal from "@/components/ModalComponent.vue";
import MovementsSummaryComponent from "@/components/Movements/MovementsSummaryComponent.vue";

export default {
  components: {
    MovementsSummaryComponent,
    Modal,
    MovementDetailComponent,
    MovementCardComponent,
  },
  data() {
    return {
      showDetail: false,
      movementDetailed: null,
      showSummary: false,
    };
  },
  props: {
    movements: {
      type: Array,
      default: () => [],
    },
  },
  methods: {
    openSummary() {
      this.showSummary = !this.showSummary;
    },
    edit(id) {
      this.close();
      this.$emit("edit", id);
    },
    remove(id) {
      this.close();
      this.$emit("remove", id);
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
      let formattedDate =
        newDate.getMonth() +
        1 +
        "/" +
        newDate.getDate() +
        "/" +
        newDate.getFullYear() +
        "  ";

      return formattedDate.toLocaleString();
    },
    openDetail(movement) {
      this.movementDetailed = movement;
      this.showDetail = true;
    },
    close() {
      this.movementDetailed = null;
      this.showDetail = false;
      this.showSummary = false;
    },
  },
};
</script>

<style scoped>
.movements {
  max-height: 100%;
  padding: 0 15px;
  margin-bottom: 14px;
  overflow: hidden;
}

.movements-header {
  display: flex;
  justify-content: space-between;
}

.title {
  margin: 8px 16px 20px 16px;
  color: white;
  font-family: Baloo, serif;
  letter-spacing: 0.1rem;
}

.content {
  max-height: 68vh;
  display: flex;
  flex-direction: column;
  gap: 8px;
  overflow-y: scroll;
}

.option {
  color: var(--brand-yellow) !important;
  cursor: pointer;
}

.default-content {
  padding: 2rem;
  height: 75vh;
  display: flex;
  text-align: center;
  justify-content: center;
  background: #f1f1f3;
  border-radius: 20px;
}
</style>

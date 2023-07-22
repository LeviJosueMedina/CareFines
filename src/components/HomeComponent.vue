<template>
  <Layout :closeMovementList="closeMovementList">
    <template #header>
      <Header @showSearch="switchShowSearch" />
      <teleport v-if="showSearch" to="#app">
        <Modal
          v-show="showSearch"
          :title="$t('titles.search')"
          :head-color="'dark-blue'"
          :body-color="'dark-blue'"
          @close="switchShowSearch"
        >
          <search-movement-component
            :movements="reactiveMovements"
            @remove="remove"
            @edit="edit"
          />
        </Modal>
      </teleport>
    </template>
    <template #resume>
      <Resume
        :total-label="$t('home.totalSaving')"
        :label="label"
        :total-amount="totalAmount"
        :amount="amount"
      >
        <template #graphic>
          <Graphic :amounts="amounts" @select="select" />
        </template>
        <template #action>
          <Action
            :editedItem="editedItem"
            :movements="reactiveMovements"
            @setDefault="setDefaultItem"
            @create="create"
            @saveEdit="saveEdit"
          />
        </template>
      </Resume>
    </template>
    <template #movements>
      <Movements :movements="reactiveMovements" @remove="remove" @edit="edit" />
    </template>
  </Layout>
</template>

<script>
import Layout from "./LayoutComponent.vue";
import Header from "./HeaderComponent.vue";
import Resume from "./Resume/ResumeComponent.vue";
import Action from "./ActionComponent.vue";
import Movements from "./Movements/MovementComponent.vue";
import Graphic from "./Resume/GraphicComponent.vue";
import Modal from "@/components/ModalComponent.vue";
import SearchMovementComponent from "@/components/Search/searchMovementComponent.vue";

export default {
  components: {
    SearchMovementComponent,
    Modal,
    Layout,
    Header,
    Resume,
    Action,
    Movements,
    Graphic,
  },
  data() {
    return {
      label: null,
      amount: null,
      movements: [],
      defaultItem: {
        title: "",
        description: "",
        amount: 0,
        time: null,
        id: null,
        category: {
          id: null,
        },
      },
      editedItem: {
        title: "",
        description: "",
        amount: 0,
        time: null,
        id: null,
      },
      showSearch: false,
      closeMovementList: false,
    };
  },
  computed: {
    reactiveMovements() {
      return this.movements;
    },
    amounts() {
      const lastDays = this.movements
        .filter((m) => {
          const today = new Date();
          const oldDate = today.setDate(today.getDate() - 30);

          return m.time > oldDate;
        })
        .map((m) => m.amount);

      return lastDays.map((m, i) => {
        const lastMovements = lastDays.slice(0, i + 1);

        return lastMovements.reduce((suma, movement) => {
          return suma + movement;
        }, 0);
      });
    },
    totalAmount() {
      return this.movements.reduce((suma, m) => {
        return suma + m.amount;
      }, 0);
    },
  },
  mounted() {
    this.orderMovements();
  },
  methods: {
    switchShowSearch() {
      this.showSearch = !this.showSearch;
    },
    orderMovements() {
      const movements = JSON.parse(localStorage.getItem("movements"));
      if (Array.isArray(movements)) {
        let movementsMapped = movements.map((m) => {
          return { ...m, time: new Date(m.time) };
        });

        this.movements = movementsMapped
          .sort(
            (a, b) => new Date(a.time).getTime() > new Date(b.time).getTime()
          )
          // .sort((a, b) => a.id - b.id)
          .reverse();
      }
    },
    create(payload) {
      this.movements.push(payload);
      this.save();
      this.orderMovements();
    },
    remove(id) {
      const index = this.movements.findIndex((m) => m.id === id);
      this.movements.splice(index, 1);
      this.save();
      this.orderMovements();
    },
    edit(id) {
      this.editedItem = this.movements.find((m) => m.id === id);
      this.orderMovements();
    },
    setDefaultItem() {
      this.editedItem = this.defaultItem;
    },
    saveEdit(payload) {
      let movement = this.movements.find((m) => m.id === payload.id);
      if (movement) {
        movement.title = payload.title;
        movement.description = payload.description;
        movement.amount = payload.amount;
        movement.category = payload.category.id;
        movement.time = payload.time;
      }
      this.save();
      this.orderMovements();
    },
    save() {
      localStorage.setItem("movements", JSON.stringify(this.movements));
    },
    select(el) {
      this.amount = el;
    },
  },
};
</script>
<style>
#app {
  background: #121f3d;
}
</style>

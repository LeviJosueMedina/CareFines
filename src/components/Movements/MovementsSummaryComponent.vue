<template>
  <main>
    <div class="content">
      <div class="data">
        <h4 class="firstElement">{{ $t("summary.totalIncome") }}</h4>
        <p>{{ totalIncome }}</p>
      </div>
      <div class="data">
        <h4>{{ $t("summary.totalExpenses") }}</h4>
        <p>{{ totalExpense }}</p>
      </div>
      <div class="data">
        <h4>{{ $t("summary.averageMonthlyIncome") }}</h4>
        <p>{{ averageMonthlyIncome }}</p>
      </div>
      <div class="data">
        <h4>{{ $t("summary.averageMonthlyExpenses") }}</h4>
        <p>{{ averageMonthlyExpense }}</p>
      </div>
      <div class="data">
        <h4>{{ $t("summary.monthHighestIncome") }}</h4>
        <p>{{ monthHighestIncome }}</p>
      </div>
      <div class="data">
        <h4>{{ $t("summary.monthHigherExpenses") }}</h4>
        <p>{{ monthHighestExpenses }}</p>
      </div>
      <div class="data">
        <h4>{{ $t("summary.biggestIncome") }}</h4>
        <p class="sub-title">
          {{ $t("labels.title") }}: {{ biggestIncome.title }}
        </p>
        <p class="amount">
          {{ $t("labels.amount") }}: {{ biggestIncome.amount }}
        </p>
      </div>
      <div class="data">
        <h4>{{ $t("summary.biggestExpense") }}</h4>
        <p class="sub-title">
          {{ $t("labels.title") }}: {{ biggestExpense.title }}
        </p>
        <p class="amount lastElement">
          {{ $t("labels.amount") }}: {{ biggestExpense.amount }}
        </p>
      </div>
    </div>
  </main>
</template>

<script>
const currencyFormatter = new Intl.NumberFormat("es-MX", {
  style: "currency",
  currency: "MXN",
});

export default {
  props: {
    movements: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      totalIncome: 0,
      totalExpense: 0,
      averageMonthlyIncome: 0,
      averageMonthlyExpense: 0,
      monthHighestIncome: null,
      monthHighestExpenses: null,
      currentYearRange: {
        firstDate: null,
        lastDate: null,
      },
      biggestIncome: {
        amount: 0,
        title: "title",
      },
      biggestExpense: {
        amount: 0,
        title: "title",
      },
    };
  },
  created() {
    this.getSummary();
  },
  methods: {
    getSummary: function () {
      this.totalIncome = "totalIncome";
      let totalIncome = 0;
      let totalExpense = 0;
      let currentYear = new Date();
      let movementsOfThisYear = [];

      //All movements
      for (let i = 0; i < this.movements.length; i++) {
        if (
          this.movements[i].time.getFullYear() === currentYear.getFullYear()
        ) {
          movementsOfThisYear.push(this.movements[i]);
        }
        if (this.movements[i].amount > 0) {
          totalIncome = this.movements[i].amount + totalIncome;
        }
        if (this.movements[i].amount < 0) {
          totalExpense = this.movements[i].amount + totalExpense;
        }
      }

      let ordered = [];

      if (Array.isArray(movementsOfThisYear)) {
        ordered = movementsOfThisYear
          .sort(function (a, b) {
            // Turn your strings into dates, and then subtract them
            // to get a value that is either negative, positive, or zero.
            return new Date(b.time) - new Date(a.time);
          })
          .reverse();
      }

      let monthNames = [
        this.$t("months.january"),
        this.$t("months.february"),
        this.$t("months.march"),
        this.$t("months.april"),
        this.$t("months.may"),
        this.$t("months.june"),
        this.$t("months.july"),
        this.$t("months.august"),
        this.$t("months.september"),
        this.$t("months.october"),
        this.$t("months.november"),
        this.$t("months.december"),
      ];

      let numberOfMonths = 0;
      let currentMonth = "";
      let totalIncomeThisYear = 0;
      let totalExpenseThisYear = 0;
      let monthHighestIncome = 0;
      let monthHighestIncomeMovement = { time: null, amount: 0, title: "" };
      let monthHighestExpense = 0;
      let monthHighestExpenseMovement = { time: null, amount: 0, title: "" };
      let monthBiggestIncome;
      let monthBiggestExpenses;
      let currentMonthIncome = 0;
      let currentMonthExpenses = 0;
      let yearlyIncomes = [];
      let yearlyExpenses = [];
      movementsOfThisYear = ordered;

      //Movements of This year
      for (let j = 0; j < movementsOfThisYear.length; j++) {
        console.log(currentMonthExpenses);
        currentMonth = currentMonth
          ? currentMonth
          : movementsOfThisYear[j].time.getMonth();

        if (
          currentMonth !== movementsOfThisYear[j].time.getMonth() ||
          j === movementsOfThisYear.length - 1
        ) {
          numberOfMonths = numberOfMonths + 1;
          yearlyIncomes.push({
            month: currentMonth,
            monthIncome: currentMonthIncome,
          });
          yearlyExpenses.push({
            month: currentMonth,
            monthExpense: currentMonthExpenses,
          });

          currentMonth = movementsOfThisYear[j].time.getMonth();

          currentMonthIncome = 0;
          currentMonthExpenses = 0;
        }

        if (movementsOfThisYear[j].amount > 0) {
          currentMonthIncome =
            movementsOfThisYear[j].amount + currentMonthIncome;

          totalIncomeThisYear =
            movementsOfThisYear[j].amount + totalIncomeThisYear;
          monthHighestIncome =
            movementsOfThisYear[j].amount > monthHighestIncome
              ? movementsOfThisYear[j].amount
              : monthHighestIncome;

          if (
            movementsOfThisYear[j].amount > monthHighestIncomeMovement.amount &&
            movementsOfThisYear[j].time > monthHighestIncomeMovement.time
          ) {
            monthHighestIncomeMovement.amount = movementsOfThisYear[j].amount;
            monthHighestIncomeMovement.title = movementsOfThisYear[j].title;
          }
        }
        if (movementsOfThisYear[j].amount < 0) {
          currentMonthExpenses =
            movementsOfThisYear[j].amount + currentMonthExpenses;

          totalExpenseThisYear =
            movementsOfThisYear[j].amount + totalExpenseThisYear;

          monthHighestExpense =
            movementsOfThisYear[j].amount < monthHighestExpense
              ? movementsOfThisYear[j].amount
              : monthHighestExpense;

          if (
            movementsOfThisYear[j].amount <
              monthHighestExpenseMovement.amount &&
            movementsOfThisYear[j].time > monthHighestExpenseMovement.time
          ) {
            monthHighestExpenseMovement.amount = movementsOfThisYear[j].amount;
            monthHighestExpenseMovement.title = movementsOfThisYear[j].title;
          }
        }
      }

      monthBiggestIncome = yearlyIncomes.reduce((previous, current) => {
        return current.monthIncome > previous.monthIncome ? current : previous;
      });

      monthBiggestExpenses = yearlyExpenses.reduce((previous, current) => {
        return current.monthExpense < previous.monthExpense
          ? current
          : previous;
      });

      this.monthHighestIncome =
        totalIncomeThisYear > 0
          ? monthNames[monthBiggestIncome.month]
          : this.$t("summary.notAvailable");

      this.monthHighestExpenses =
        totalExpenseThisYear < 0
          ? monthNames[monthBiggestExpenses.month]
          : this.$t("summary.notAvailable");

      this.biggestIncome.title =
        monthHighestIncomeMovement.amount > 0
          ? monthHighestIncomeMovement.title
          : this.$t("summary.notAvailable");
      this.biggestIncome.amount = currencyFormatter.format(
        monthHighestIncomeMovement.amount
      );
      this.biggestExpense.title =
        monthHighestExpenseMovement.amount < 0
          ? monthHighestExpenseMovement.title
          : this.$t("summary.notAvailable");
      this.biggestExpense.amount = currencyFormatter.format(
        monthHighestExpenseMovement.amount
      );

      this.averageMonthlyIncome =
        totalIncomeThisYear > 0
          ? currencyFormatter.format(totalIncomeThisYear / numberOfMonths)
          : "$0.00";
      this.averageMonthlyExpense = totalExpenseThisYear
        ? currencyFormatter.format(totalExpenseThisYear / numberOfMonths)
        : "$0.00";

      this.totalIncome = currencyFormatter.format(totalIncome);
      this.totalExpense = currencyFormatter.format(totalExpense);
    },
  },
};
</script>

<style scoped>
main {
  margin: 0;
  padding: 3rem;
}

.data {
  display: flex;
  flex-direction: column;
}

h4,
p {
  margin: 0;
  color: white;
}

p {
  padding: 20px 0 2rem 0;
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

.content {
  border-left: 5px solid var(--brand-yellow);
  padding-left: 2rem;
}

.lastElement {
  padding-bottom: 0.5rem;
}
.firstElement {
  padding-bottom: 0.5rem;
}

.sub-title {
  padding-bottom: 0;
}
.amount {
  padding-top: 0.5rem !important;
}
</style>

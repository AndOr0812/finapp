<script>
import Amount from '@/components/amount/Amount'
import TrnItem from '@/components/budgets/BudgetItemTrn'
import TrnsList from '@/components/trns/list/TrnsList2'
import { formatDate } from '@/utils/formatDate'

export default {
  components: {
    Amount,
    TrnItem,
    TrnsList
  },

  props: {
    id: {
      type: String,
      required: true
    },

    budget: {
      type: Object,
      required: true
    }
  },

  data () {
    return {
      isTrnsVisible: false
    }
  },

  computed: {
    formatedDate () {
      return formatDate(this.budget.date, 'number')
    },

    trnsIds () {
      if (this.budget.trnsIds) {
        return Object.keys(this.budget.trnsIds)
          .sort((a, b) => {
            if (this.$store.state.trns.items[a].date > this.$store.state.trns.items[b].date) return -1
            if (this.$store.state.trns.items[a].date < this.$store.state.trns.items[b].date) return 1
            return 0
          })
      }
    },

    gotAmount () {
      if (this.budget.trnsIds) {
        return Math.abs(this.$store.getters.getTotalOfTrnsIds(this.trnsIds).incomes)
      }
      return 0
    },

    styles () {
      return {
        width: `${Math.abs(this.gotAmount) / Math.abs(this.budget.amount) * 100}%`
      }
    }
  },

  methods: {
    async removeBudgetId (id) {
      // await this.$store.dispatch('removeBudget', id)
    },

    handleTrnItemClick (trnId) {
      this.$store.commit('hideCategoryModal')
      this.$store.commit('showTrnModal')
      this.$store.commit('setTrnModalId', trnId)
    }
  }
}
</script>

<template lang="pug">
.budgetItem(@click="removeBudgetId(budget.id)")
  .budgetItem__countTotal(v-if="budget.countTotal")

  .budgetItem__top(@click="isTrnsVisible = !isTrnsVisible")
    .budgetItem__meta
      .budgetItem__name {{ budget.name }}
      .budgetItem__description(v-if="budget.description") {{ budget.description }}

    .budgetItem__total
      .sum._right
        .sum__title {{ $lang.budgets.stat.total }}
        .sum__amount
          Amount(:currency="budget.currency" :value="budget.amount")

  //- .budgetItem__hours(v-if="budget.spendHours")
    div {{ Math.abs(budget.amount / budget.spendHours).toFixed() }} in hour of {{ budget.spendHours }} hours

  .budgetItem__info(@click="isTrnsVisible = !isTrnsVisible")
    .budgetItem__amounts
      .sum
        .sum__title {{ $lang.budgets.stat.left }}
        .sum__amount
          Amount(:currency="budget.currency" :value="budget.amount - gotAmount")
      .sum._right
        .sum__title {{ $lang.budgets.stat.got }}
        .sum__amount
          Amount(:currency="budget.currency" :type="1" :value="gotAmount")

    .budgetItem__graph: .budgetItem__graph__in(:style="styles")

  .budgetItem__trns(v-if="budget.trnsIds" v-show="isTrnsVisible")
    TrnsList(:ids="trnsIds" v-slot="{ trns }")
      TrnItem(
        v-for="trnItem in trns"
        :category="trnItem.category"
        :id="trnItem.id"
        :key="trnItem.id"
        :trn="trnItem.trn"
        :wallet="trnItem.wallet")
  .budgetItem__noTrns(v-else v-show="isTrnsVisible")
    h1 No trns
</template>

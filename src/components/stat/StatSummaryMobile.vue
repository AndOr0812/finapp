<script>
import Amount from '@/components/amount/Amount'

export default {
  components: {
    Amount
  },

  computed: {
    stat () {
      return this.$store.getters.statCurrentPeriod
    },
    statAverage () {
      return this.$store.getters.statAverage
    },
    period () {
      return this.$store.state.filter.period
    }
  }
}
</script>

<template lang="pug">
.summary
  .summary__item._incomes(@click="$store.dispatch('toogleVisibleCatsChart')")
    template(v-if="statAverage.incomes > 0 || period === 'all'")
      .summary__item__title._incomes {{ $lang.money.incomes }}
      .summary__item__amount
        Amount(
          :center="true"
          :currency="$store.state.currencies.base"
          :type="1"
          :value="stat.incomes.total")
    template(v-else) .

  .summary__item._total(@click="$store.dispatch('toogleVisibilityStatItems')")
    template(v-if="statAverage.total !== 0")
      template(v-if="stat.incomes.total > 0 || stat.expenses.total > 0")
        .summary__item__title {{ $lang.money.total }}
        .summary__item__amount
          Amount(
            :center="true"
            :currency="$store.state.currencies.base"
            :value="stat.incomes.total - stat.expenses.total")
    template(v-else) .

  .summary__item._expenses(@click="$store.dispatch('toogleShowStatGraphs')")
    template(v-if="statAverage.expenses > 0 || period === 'all'")
      .summary__item__title._expenses {{ $lang.money.expenses }}
      .summary__item__amount
        Amount(
          :center="true"
          :currency="$store.state.currencies.base"
          :value="stat.expenses.total"
          :type="0")
    template(v-else) .
</template>

<style lang="stylus" scoped>
@import "~@/stylus/variables/margins"

.summary
  display flex
  padding $m7 0
  background var(--c-bg-4)
  border-bottom 1px solid var(--c-bg-1)

  &__item
    flex 1
    text-align center
    display flex
    flex-flow column
    align-items center
    justify-content center

    &:first-child
      align-items flex-start
      padding-left $m7

    &:last-child
      align-items flex-end
      padding-right $m7

    &__title
      padding-bottom $m5
      color var(--c-font-4)
</style>

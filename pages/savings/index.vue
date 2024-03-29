<template>
  <div style="position: relative; height: 100%;">
    <v-row>
      <v-col cols="12">
        <PageTitle disable-back title="Tujuan Tabungan" />
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="12" class="py-0">
        <v-chip outlined @click="$router.push('/savings/edit-savings-amount')">
          <v-icon class="mr-3">
            mdi-calendar
          </v-icon>
          Edit Frekuensi Nabung
        </v-chip>

        <div class="text-center mt-3">
          <div>Frekuensi Nabung</div>
          <h1 class="primary--text">
            Rp 1.000.000/bulan
          </h1>
        </div>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="12" class="">
        <template v-for="item of items">
          <v-sheet
            :key="item.id"
            outlined
            class="mb-3 pa-3"
          >
            <div style="font-size: 16px;" class="font-weight-bold">
              {{ item.name }}
            </div>

            <div style="font-size: 13px;">
              {{ item.days }} hari lagi
            </div>

            <v-progress-linear class="my-1" height="10" rounded color="accent" :value="getExpensePercentage(item)" />

            <div style="font-size: 13px;">
              Terkumpul
            </div>

            <div class="subtitle-1">
              Rp{{ $utils.numberToLocaleString(item.temp_expense) }} dari Rp{{ $utils.numberToLocaleString(item.expense) }}
            </div>
          </v-sheet>
        </template>
      </v-col>
    </v-row>

    <v-fab-transition>
      <v-btn
        color="success"
        fab
        style="position: fixed; right: 10px; bottom: 66px;"
        to="/savings/add-target"
      >
        <v-icon>mdi-plus</v-icon>
      </v-btn>
    </v-fab-transition>
  </div>
</template>

<script>
import PageTitle from '@/components/PageTitle'

export default {
  name: 'BudgetIndex',

  components: {
    PageTitle
  },

  data: () => ({
    items: []
  }),

  mounted () {
    this.fetch()
  },

  methods: {
    toMonthName (dateString) {
      const date = new Date(dateString)

      return date.toLocaleString('id-ID', {
        month: 'long'
      })
    },

    getDaysRemaining (year, month) {
      const now = new Date()
      const budgetDate = new Date(`${year}-${month}`)
      if (Date.parse(budgetDate) < Date.parse(now)) {
        return null
      }

      const budgetDateEnd = this.$moment(budgetDate).add('month', 1)

      const diff = budgetDateEnd.diff(this.$moment(now), 'days')
      return diff + ' Hari'
    },

    getExpensePercentage (item) {
      return Math.floor((item.temp_expense / item.expense) * 100)
    },

    async fetch () {
      this.loading = true
      try {
        const { data } = await this.$axios.get('/inf/api/target-list')

        this.items = data.data.target.map(item => ({
          id: item.id,
          name: item.name,
          expense: Number(item.expense),
          daily_payment: Number(item.daily_payment),
          percentage: Number(item.percentage),
          temp_expense: Number(item.temp_expense),
          temp_percentage: Number(item.temp_percentage)
        }))

        this.$utils.getDays(this.items)
      } catch (e) {
        console.error(e)
      }
    }
  }
}
</script>

<style scoped>
.budget-summary {
  position: absolute;
  text-align: center;
  width: 100%;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>

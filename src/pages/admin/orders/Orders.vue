<template>
  <div class="orders-table flex">
    <va-card class="flex mb-4">
      <va-card-title>{{ t('tables.basic') }}</va-card-title>
      <va-card-content>
        <div class="table-wrapper">
          <table class="va-table va-table--striped va-table--hoverable">
            <thead>
              <tr>
                <th>{{ t('tables.headings.email') }}</th>
                <th>{{ t('tables.headings.name') }}</th>
                <th>{{ t('tables.headings.date') }}</th>
              </tr>
            </thead>

            <tbody>
              <tr v-for="order in orders" :key="order.orderId">
                <td>{{ order.userEmail }}</td>
                <td>{{ order.orderId }}</td>
                <td>{{ order.date }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </va-card-content>
    </va-card>
  </div>
</template>

<script setup lang="ts">
  import { computed, onMounted, ref } from 'vue'
  import { getFirebaseOrders } from '../../../firebase'
  import dayjs from 'dayjs'
  import relativeTime from 'dayjs/plugin/relativeTime'
  import { useI18n } from 'vue-i18n'
  dayjs.extend(relativeTime)

  const { t } = useI18n()
  const ordersFromApi = ref([])
  const orders = computed(() => {
    return Object.entries(ordersFromApi.value).map(([orderId, order]) => {
      return {
        orderId,
        date: dayjs(order.date).fromNow(),
        userEmail: order.email,
      }
    })
  })

  onMounted(async () => {
    ordersFromApi.value = await getFirebaseOrders()
    console.log('ordersFromApi', ordersFromApi.value)
  })
</script>

<style scoped>
  .orders-table {
    .table-wrapper {
      overflow: auto;
    }

    .va-table {
      width: 100%;
    }
  }
</style>

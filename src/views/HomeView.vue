<template>
  <div>

    <div class="q-pa-md flex flex-center">
      <q-circular-progress
          :max="homeData.releasedProduct+homeData.unReleasedProduct"
          :min="homeData.releasedProduct+homeData.unReleasedProduct"
          :value="homeData.releasedProduct+homeData.unReleasedProduct"
          class="text-light-blue q-ma-md"
          color="light-blue"
          font-size="60px"
          show-value
          size="150px"
      />
      <q-circular-progress
          :max="homeData.releasedProduct+homeData.unReleasedProduct"
          :min="0"
          :value="homeData.unReleasedProduct"
          class="text-light-blue q-ma-md"
          color="light-blue"
          font-size="60px"
          show-value
          size="150px"
      />
      <q-circular-progress
          :max="homeData.releasedProduct+homeData.unReleasedProduct"
          :min="0"
          :value="homeData.releasedProduct"
          class="text-light-blue q-ma-md"
          color="light-blue"
          font-size="60px"
          show-value
          size="150px"
      />
      <q-circular-progress
          :max="homeData.subscription"
          :min="homeData.subscription"
          :value="homeData.subscription"
          class="text-light-blue q-ma-md"
          color="light-blue"
          font-size="60px"
          show-value
          size="150px"
      />


    </div>

    <div>
      <h4>Products</h4>
      <ProductList :mySize="5"/>
    </div>
    <p/>
    <div>
      <h4>Subscriptions</h4>
      <SubscriptionList :mySize="5"/>
    </div>
  </div>
</template>

<script lang="ts" setup>
import {useQuasar} from 'quasar'
import {onMounted, reactive} from 'vue'
import SubscriptionList from '@/components/SubscriptionList.vue'
import ProductList from '@/components/ProductList.vue'
import api, {Statistic} from "@/common/api";

const homeData = reactive<Statistic>({
  releasedProduct: 0,
  unReleasedProduct: 0,
  subscription: 0,
})

function fetchData() {
  api.statistic.data().then(data => {
    console.log("response:", data)
    if (data.code == 0) {
      console.log(data.data);
      homeData.releasedProduct = data.data.releasedProduct;
      homeData.unReleasedProduct = data.data.unReleasedProduct;
      homeData.subscription = data.data.subscription;
    } else {
      useQuasar().notify({
        message: data.message,
        color: 'primary',
        position: 'top'
      })
    }

  })
}

onMounted(() => {
  fetchData()
})
</script>
  

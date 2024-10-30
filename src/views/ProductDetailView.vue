<template>
  <div class="q-pa-md q-gutter-sm">
    <q-breadcrumbs>
      <q-breadcrumbs-el icon="home" label="Home" to="/"/>
      <q-breadcrumbs-el icon="widgets" label="Products" to="/product"/>
      <q-breadcrumbs-el label="Product Details"/>
    </q-breadcrumbs>
  </div>
  <q-page class="padding">
    <q-card class="my-card text-white">
      <q-card-section>
        <div class="text-h6">Product Details</div>
        <div class="text-subtitle1">
        </div>
      </q-card-section>
      <q-separator inset/>
      <q-card-section class="column q-gutter-md">
        <div class="q-pa-md example-row-column-width">
          <div class="row">
            <div class="col">
              Name
            </div>
            <div class="col-11">
              {{ product.name }}
            </div>
          </div>
          <div class="row">
            <div class="col">
              Product Description
            </div>
            <div class="col-11">
              {{ product.description }}
            </div>
          </div>
          <div class="row">
            <div class="col">
              Product price
            </div>
            <div class="col-11">
              {{ product.price }}
            </div>
          </div>
          <div class="row">
            <div class="col">
              Product Image
            </div>
            <div class="col-11">
              {{ product.imageURL }}
            </div>
          </div>
          <div class="row">
            <div class="col">
              Product Released
            </div>
            <div class="col-11">
              <q-toggle v-model="product.isRelease" disable/>
            </div>
          </div>
        </div>
      </q-card-section>
      <q-card-section class="column q-gutter-md">
        <q-list bordered class="rounded-borders" style="max-width: 60%">
          <q-item-label v-if="product.reviews.length>0" header>Reviews</q-item-label>
          <q-item-label v-else header>No Reviews</q-item-label>
          <q-item v-for="review in product.reviews" :key="review.id" v-ripple clickable>
            <q-item-section>
              <q-item-label>{{ review.author }}</q-item-label>
              <q-item-label caption lines="2">{{ review.comment }}</q-item-label>
            </q-item-section>
            <q-item-section side top>
              <q-item-label caption>{{ review.createdAt }}</q-item-label>
              <q-rating v-model="review.rating" color="yellow" icon="star_border" icon-selected="star" max="5" readonly
                        size="xs"/>
            </q-item-section>
            <q-separator inset spaced/>
          </q-item>


        </q-list>
      </q-card-section>
      <q-card-section class="column q-gutter-md">
        <q-list bordered class="rounded-borders" style="max-width: 60%">
          <q-item-label header>Add review</q-item-label>
          <q-item-section>
            <q-input v-model="newReview.author" :rules="[ val => val && val.length > 0 || 'Please type something']" filled label="Your name *"
                     lazy-rules/>
            <q-rating v-model="newReview.rating" color="yellow" icon="star_border" icon-selected="star" max="5"
                      no-dimming size="3.5em"/>
            <q-input v-model="newReview.comment" :rules="[ val => val && val.length > 0 || 'Please type something']" filled label="Review *"
                     lazy-rules/>
            <div>
              <q-btn color="primary" label="Add" type="button" @click="addReview"/>
            </div>
          </q-item-section>
        </q-list>
      </q-card-section>
    </q-card>
  </q-page>

</template>
<script lang="ts" setup>
import {computed, onMounted, reactive} from 'vue'
import {useRoute} from 'vue-router'
import api, {Product, ProductReview} from "@/common/api"
import {Notify} from 'quasar'

const route = useRoute()
const slug = computed<string>(() => route.params.slug as string)
const product = reactive<Product>({
  name: '',
  description: '',
  isRelease: false,
  price: 0,
  imageURL: '',
  reviews: []
})

const newReview = reactive<ProductReview>({
  id: '',
  productId: Number(slug.value),
  rating: 0,
  author: '',
  comment: '',
})

function addReview() {
  api.productReview.add(slug.value, newReview).then(response => {
    if (response.code == 0) {
      Notify.create({
        message: 'Congrats, this review has been added successfully!',
        color: 'green',
        position: 'top'
      })
      fetchReviews(slug.value)
      newReview.author = ''
      newReview.comment = ''
      newReview.rating = 0
    } else {
      Notify.create({
        message: response.message,
        color: 'red',
        position: 'top'
      })
    }
  })
}

function fetchProduct(slug: string) {
  api.products.details(slug).then(response => {
    if (response.code == 0) {
      product.name = response.data.name
      product.description = response.data.description
      product.price = response.data.price
      product.imageURL = response.data.imageURL
      product.isRelease = response.data.isRelease
    } else {
      Notify.create({
        message: response.message,
        color: 'red',
        position: 'top'
      })
    }
  })
  fetchReviews(slug)
}

function fetchReviews(slug: string) {
  api.productReview.reviews(slug).then(response => {
    if (response.code == 0) {
      console.log("get reviews", response.data)
      product.reviews = response.data
    }
  })
}

onMounted(() => {
  if (slug.value)
    fetchProduct(slug.value)
})
</script>
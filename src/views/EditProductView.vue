<template>
  <div class="q-pa-md q-gutter-sm">
    <q-breadcrumbs>
      <q-breadcrumbs-el icon="home" label="Home" to="/"/>
      <q-breadcrumbs-el icon="widgets" label="Products" to="/product"/>
      <q-breadcrumbs-el :label="title"/>
    </q-breadcrumbs>
  </div>
  <q-page class="padding">
    <q-card class="my-card text-white">
      <Form :validation-schema="schema">
        <q-card-section>
          <div class="text-h6">{{ title }}</div>
          <div class="text-subtitle1">
          </div>
        </q-card-section>
        <q-separator inset/>
        <q-card-section class="column q-gutter-md">
          <Field v-slot="{field}" name="name" type="text">
            <q-input id="name" v-model="product.name" label="Product name" outlined v-bind="field"/>
          </Field>
          <error-message name="name"/>
          <Field v-slot="{field}" name="description" type="text">
            <q-input id="description" v-model="product.description" label="Product Description" outlined
                     v-bind="field"/>
          </Field>
          <error-message name="description"/>
          <Field v-slot="{field}" name="price" type="text">
            <q-input id="price" v-model="product.price" label="Product price" outlined v-bind="field"/>
          </Field>
          <error-message name="price"/>
          <Field v-slot="{field}" name="imageURL" type="text">
            <q-input id="imageURL" v-model="product.imageURL" label="Product Image" outlined v-bind="field"/>
          </Field>
          <ErrorMessage name="imageURL"/>
          <q-toggle v-model="product.isRelease" label="Product Released"/>
        </q-card-section>
        <q-card-actions align="right">
          <q-btn flat @click="$router.push({name:'Product'})">Cancel</q-btn>
          <q-btn color="primary" type="button" @click="submitForm">{{ title }}</q-btn>
        </q-card-actions>
      </Form>
    </q-card>
  </q-page>
</template>

<script lang="ts" setup>
import {computed, onMounted, reactive} from 'vue'
import {useRoute, useRouter} from 'vue-router'
import api, {APIResponse, Product} from "@/common/api"
import {Notify} from 'quasar'
import {ErrorMessage, Field, Form} from 'vee-validate';
import * as yup from 'yup';

const route = useRoute()
const router = useRouter()
const slug = computed<string>(() => route.params.slug as string)
const title = slug.value ? 'Update Product' : 'Add Product'
const product = reactive<Product>({
  name: '',
  description: '',
  isRelease: false,
  price: 0,
  imageURL: ''
})

const schema = yup.object().shape({
  name: yup.string().required().min(3).max(255),
  description: yup.string().max(255),
  price: yup.number().positive().integer(),
  imageURL: yup.string().max(255)
});

const submitForm = () => {
  try {
    schema.validateSync(product)
    console.log(schema.isValid(product).then())
    console.log('submit!', slug, product)
    let responsePromise: Promise<APIResponse<Product>>
    if (!slug.value) {
      responsePromise = api.products.add(product)
    } else {
      responsePromise = api.products.update(slug.value, product)
    }
    responsePromise.then(response => {
      console.log(response.data)
      if (response.code == 0) {
        Notify.create({
          message: 'Congrats, this product has been added successfully!',
          color: 'green',
          position: 'top'
        })
      } else {
        Notify.create({
          message: response.message,
          color: 'red',
          position: 'top'
        })
      }
    })
    router.push('/product')
  } catch (error) {
    console.log("error", error);
    Notify.create({
      message: "Oops, Please check the input." + error,
      color: 'red',
      position: 'top'
    })
  }
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
}

onMounted(() => {
  if (slug.value)
    fetchProduct(slug.value)
})


</script>

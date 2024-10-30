<script setup>
import {RouterView} from 'vue-router'
import Header from './components/DemoHeader.vue'
import Footer from './components/DemoFooter.vue'
</script>

<template>
  <div class="q-pa-md">
    <q-layout class="shadow-2 rounded-borders" view="lHh lpr lFf">
      <Header/>
      <Footer/>
      <q-page-container>
        <RouterView/>
      </q-page-container>
    </q-layout>
  </div>
</template>

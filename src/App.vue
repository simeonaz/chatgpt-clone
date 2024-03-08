<script setup>
import { RouterView } from 'vue-router'
import AppSide from './components/AppSide.vue'
</script>

<template>
  <div class="md:grid md:grid-cols-5 h-lvh">
    <AppSide />

    <div class="md:col-span-4 h-full flex flex-col bg-[#191919] relative overflow-y-auto">
      <RouterView />
    </div>
  </div>
</template>

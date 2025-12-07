<template>
  <div class="home-page">
    <h1>Калькулятор подсетей</h1>
    <SubnetCalculator />
  </div>
</template>

<script setup lang="ts">
import SubnetCalculator from "@/components/SubnetCalculator.vue";
</script>

<style scoped>
.home-page {
  padding: 20px;
  font-family: Arial, sans-serif;
}

h1 {
  text-align: center;
  color: #2c3e50;
}
</style>

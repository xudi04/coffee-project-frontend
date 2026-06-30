<template>
  <div class="home-page">
    <AppHeader />
    <main class="main-content">
      <HeroContent id="hero" />
      <AboutStory id="about" />
      <CoffeeProducts id="coffees" />
      <StoreBranches id="branches" />
      <AppFooter />
    </main>

  </div>
</template>

<script setup>
import AppHeader from './components/layout/AppHeader.vue'
import HeroContent from './components/layout/HeroContent.vue'
import AboutStory from './components/layout/AboutStory.vue'
import CoffeeProducts from './components/layout/CoffeeProducts.vue'
import StoreBranches from './components/layout/StoreBranches.vue'
import AppFooter from './components/layout/AppFooter.vue'
</script>

<style scoped>
.home-page {
  width: 100%;
  min-height: 100vh;
  background-color: var(--bg-antrasit);
}

.main-content {
  padding-top: 90px; 
  width: 100%;
  position: absolute;
  left: 0px;
  top:0px; 
}
</style>
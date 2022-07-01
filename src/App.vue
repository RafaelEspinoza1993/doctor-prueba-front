<template>
  <div id="app">
    <NavbarComponent />
    <MainComponent />
  </div>
</template>

<script>
import MainComponent from './components/MainComponent.vue'
import NavbarComponent from './components/NavbarComponent.vue'

export default {
  name: 'App',
  components: {
    MainComponent,
    NavbarComponent
  }
}
</script>

<style>
</style>

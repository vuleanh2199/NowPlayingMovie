<template>
  <div class='app-content'>
    <top-bar></top-bar>
    <router-view/>
  </div>
</template>

<style>
 .app-content {
   height: 100%;
 }
</style>

<script>
import TopBar from './components/TopBar.vue'
export default {
  components: {
    'top-bar': TopBar
  }
}
</script>

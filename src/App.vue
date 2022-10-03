<template>
  <div class="app">
    <AutoCalculator />
  </div>
</template>

<script>
import AutoCalculator from './views/AutoCalculator.vue';
export default {
  name: 'App',
  components: { AutoCalculator },
};
</script>

<style lang="scss" scoped>
@import './assets/style/fonts.scss';
@import './assets/style/main.scss';
* {
  margin: 0;
  padding: 0;
}
.app {
  font-family: 'Nekst', 'Gilroy';
}
</style>

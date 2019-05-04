<template>
  <div id="app">
    <Roller/>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import Roller from './components/Roller.vue';

@Component({
  components: {
    Roller,
  },
})
export default class App extends Vue {}
</script>

<style>
#app {
  font-family: 'Bitter', serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: rgb(87, 85, 85);
  margin-top: 60px;
}
</style>

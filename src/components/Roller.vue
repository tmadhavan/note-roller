<template>
  <div class="container">

    <transition name="slide">
    <div v-show="currentNote && currentDirection" class="outputContainer">
      <transition name="fade" mode="out-in">
      <div class="currentOutput" :key="currentDirection"> {{ currentDirection }} </div>
      </transition>
      <transition name="fade" mode="out-in">
      <div class="currentOutput" :key="currentNote"> {{ currentNote }} </div>
      </transition>
    </div>
    </transition>

    <div class="rollButtonContainer">
      <button class="rollbutton" @click="roll"> Roll </button>
    </div>
  </div>
</template>


<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';


@Component
export default class Roller extends Vue {

  currentNote: string | null = null; 
  currentDirection: string | null = null; 
  
  noteMap: { [key: number]: string } = {
    0: 'B',
    1: 'C',
    2: 'C♯',
    3: 'D',
    4: 'E♭',
    5: 'E',
    6: 'F',
    7: 'F♯',
    8: 'G',
    9: 'G♯',
    10: 'A',
    11: 'B♭',
  };

  directionMap: { [key: number]: string } = {
    0: '◀',
    1: '▲',
    2: '▶',
    3: '▼',
  };

  private randomInt(max: number): number {
    return Math.floor(Math.random() * Math.floor(max));
  }

  private roll() {
    this.currentNote = this.noteMap[this.randomInt(12)];
    this.currentDirection = this.directionMap[this.randomInt(4)];
  }
}
</script>

<style lang="css" scoped>

.container {
  min-width: 480px;
  min-height: 500px;
  padding: 2em;
  margin: 0 auto;
  height: 80vh;
  width: 80vw;
  display: flex;
  flex-direction: column;
  flex-wrap: nowrap;
  justify-content: center;

}

.outputContainer {
    height: 8em;
    display: flex;
    justify-content: center;
}

.rollButtonContainer {
    height: 5em;
    font-size: 2em;
    padding: 2em; 
}

.rollButtonContainer button { 
  height: 2em;
  border: none;
  vertical-align: middle;
  line-height: 0.9;
  padding: 0.2em 0.5em;
  text-align: center;
  font-size: 2em;
  background: darkgrey;
  font-family: 'Bitter', serif;
  color: white;
  box-shadow: none;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  -webkit-appearance: none;

}

button:focus {
  outline: none;
}

button:hover { 
  background: gray;
  color: white;
}


.currentOutput {
  padding: 0.5em;
  font-size: 3em;
  flex-basis: 10%;
}

.fade-enter-active { 
  transition: all .2s; 
}

.fade-leave-active {
  transition: all .2s; 
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}

.slide-enter, .slide-leave-active {
  height: 0;
}

.slide-enter-active { 
  transition: all 0.8s ease; 
}

.slide-leave-active {
  transition: all 0.8s ease; 
}

</style>
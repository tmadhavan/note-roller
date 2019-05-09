<template>
  <div class="container">

    <transition name="slide">
    <div v-show="currentNote && currentDirection" class="outputContainer">
      <transition name="fade" mode="out-in">
      <div class="currentOutput" :key="currentNote"> {{ currentNote }} </div>
      </transition>
      <transition name="fade" mode="out-in">
        <div class="currentOutput" :key="currentDirection"> {{ currentDirection }} </div>
      </transition>
    </div>
    </transition>
    <div>
      <transition name="fade" mode="out-in">
        <div class="currentlyRollingText" v-show="currentlyAutoRolling"> Re-rolling in {{ this.remainingDelay }}s </div>
      </transition>
    </div>
    <div class="rollButtonContainer">
      <transition name="slide" mode="out-in">
        <button class="rollbutton" @click="rollButtonClicked"> {{ rollButtonText }} </button>
      </transition>
    </div>

    <div class="optionsContainer">
      <div class="notesOptions">
        <CheckOptions :options="notes" name="Notes" @optionChanged="selectedNotesChanged"></CheckOptions>
      </div>

      <div class="directionsOptions">
        <CheckOptions :options="directions" name="Directions" @optionChanged="selectedDirectionsChanged"></CheckOptions>
      </div>

      <div class="autoRollOptions">

      <div class="delayOptions">
        <div> Auto-roll </div>
        <div class="autoRollDelayInput">
          <input id="autoRollEnabled" type="checkbox" v-model="autoRoll" :value="autoRoll">
          <span><input type="number" :disabled="!autoRoll || (autoRoll && currentlyAutoRolling)"
                       min="1" max="100" size="5"
                       @blur="checkDelayInput"
                       placeholder="Roll delay (default: 3s)"
                       v-model="autoRollDelay"> seconds </span>
        </div>
      </div>

      <div class="delayWarnOptions" v-show="autoRoll">
        <div> Warning beep </div>
        <div class="delayWarnInput">
          <input id="delayWarnEnabled" type="checkbox" v-model="delayWarn" :value="delayWarn">
          <span><input type="number" :disabled="!autoRoll || !delayWarn"
                       min="1" max="100" size="5"
                       @blur="checkWarnInput"
                       placeholder="Roll delay (default: 3s)"
                       v-model="delayWarnTime"> seconds </span>
        </div>


      </div>
      </div>

    </div>
  </div>

</template>


<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import CheckOptions from "@/components/CheckOptions.vue";


@Component({
  components: {
    CheckOptions
  }})
export default class Roller extends Vue {

  private readonly notes: string[] = ['B', 'C', 'C♯', 'D', 'E♭', 'E', 'F', 'F♯', 'G', 'G♯',  'A', 'B♭'];
  private readonly directions: string[] = ['◀',  '▲', '▶', '▼'];
  private readonly DEFAULT_ROLL_DELAY_S = 3;

  // Currently displayed note/direction
  currentNote: string | null = null; 
  currentDirection: string | null = null; 
  
  // Notes that have been selected via checkboxes in OptionBar components 
  selectedNotes: string[] = this.notes;
  selectedDirections: string[] = this.directions;
  
  // If auto roll is enabled, the notes progress without click inputs  
  autoRoll: boolean = false;
  autoRollDelay: number = this.DEFAULT_ROLL_DELAY_S;
  remainingDelay: number = -1;
  currentlyAutoRolling: boolean = false; 
  autoRollingInterval: number = -1;
  countdownInterval: number = -1;

  // Sound a warning beep at a specified point in the countdown
  delayWarn: boolean = false;
  delayWarnTime: number = this.DEFAULT_ROLL_DELAY_S;

  beep = require('beepbeep');

  get rollButtonText(): string {
    if (this.currentlyAutoRolling) {
      return "Stop";
    } else if (this.autoRoll) {
      return "Start"; 
    } else {
      return "Roll";
    }
  }

  private randomInt(max: number): number {
    return Math.floor(Math.random() * Math.floor(max));
  }

  private selectedNotesChanged(notes: string[]) {
    console.log(`Notes changed ${JSON.stringify(notes)}`)
    if (notes.length == 0) {
      this.selectedNotes = this.notes; 
    } else {
      this.selectedNotes = notes;
    }
  }

  private selectedDirectionsChanged(directions: string[]) {
    console.log(`Directions changed: ${JSON.stringify(directions)}`)
    if (directions.length == 0) {
      this.selectedDirections = this.directions;
    } else {
      this.selectedDirections = directions;
    }
  }

  private rollButtonClicked(): void { 
    if (this.autoRoll && !this.currentlyAutoRolling) { 
      this.startAutoRolling(); 
    } else if (this.currentlyAutoRolling) {
      this.stopAutoRolling();
    } else {
      this.roll();
    }
  }

  private stopAutoRolling(): void { 
    window.clearInterval(this.autoRollingInterval);
    window.clearInterval(this.countdownInterval);
    this.currentlyAutoRolling = false;
  }

  private updateCountdown(): void {
    if (this.remainingDelay - 1 == 0) {
      this.remainingDelay = this.autoRollDelay;
    } else {
      if (this.delayWarn && this.remainingDelay - 1 == this.delayWarnTime) {
        console.log("Warning reached");
        this.beep();
      }
      this.remainingDelay--;
    }
  }

  private startAutoRolling(): void {
    this.currentlyAutoRolling = true;
    this.remainingDelay = this.autoRollDelay;
    this.countdownInterval = window.setInterval(() => this.updateCountdown(), 1000);
    this.autoRollingInterval = window.setInterval(() => this.roll(), this.autoRollDelay * 1000);
    this.roll();
  }

  private roll() {
    const newNote = this.selectedNotes[this.randomInt(this.selectedNotes.length)] || "";
    const newDirection = this.selectedDirections[this.randomInt(this.selectedDirections.length)] || "";

    if (newNote !== this.currentNote && newDirection !== this.currentDirection) {
      this.currentNote = newNote;
      this.currentDirection =  newDirection;
    } else {
      this.roll();
    }

  }

  /**
   * Simple 'validation' to ensure that the number input for the delay value is 1 <= d <= 30
   */
  private checkDelayInput(): void {
    if (this.autoRollDelay < 1) {
      this.autoRollDelay = 1; 
    } 
    if (this.autoRollDelay > 30) {
      this.autoRollDelay = 30; 
    }
  }

  /**
   * Ensure the waring sound time is less than the auto roll delay time
   */
  private checkWarnInput(): void {
    if (this.delayWarnTime < 1) {
      this.delayWarnTime = 1;
    }

    if (this.delayWarnTime > this.autoRollDelay) {
      this.delayWarnTime = this.autoRollDelay;
    }
  }

  beforeDestroy() {
    if (this.autoRollingInterval != -1) {
      window.clearInterval(this.autoRollingInterval);
    }
    if (this.countdownInterval != -1) {
      window.clearInterval(this.countdownInterval);
    }
  }
}
</script>

<style lang="scss" scoped>

.container {
  min-width: 850px;
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
    height: 10em;
    display: flex;
    font-size: 2em;
    justify-content: center;
}

.rollButtonContainer {
    height: 3em;
    font-size: 1.5em;
    padding: 2em;
    clear: both;

    & .currentlyRollingText {
      font-size: 0.8em;
      margin-bottom: 30px;
    }

    & button {
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

      &:focus {
        outline: none;
      }

      &:hover {
        background: gray;
        color: white;
      }
    }
}

.currentOutput {
  padding: 0.5em;
  font-size: 3em;
  flex-basis: 10%;
  line-height: 1.9;
}

.optionsContainer {
  padding-top: 20px;
  display: flex;
  flex-direction: column;
  font-size: 0.9em;
}

.notesOptions {
  height: 50px;
}

.autoRollOptions {
  flex-basis: 50%;
  display: flex;
  flex-direction: row;
  line-height: 1.9;
  font-size: 0.8em;
  text-align: left;
  margin-left: 30%;

  & label { 
    text-align: left;
  }

  & .autoRollDelayInput {
    font-size: 0.8em;
  }

  & .delayOptions {
    padding: 0.4em;

  }

  & .delayWarnOptions {
    padding: 0.4em;
  }

  & .delayWarnInput {
    font-size: 0.8em;
  }
}


/*.delayWarnOptions {*/
/*  flex-basis: 50%;*/
/*  line-height: 1.9;*/
/*  font-size: 0.8em;*/
/*  text-align: left;*/
/*  margin-left: 30%;*/

/*  & label {*/
/*    text-align: left;*/
/*  }*/


/*}*/

.directionsOptions {
  flex-basis: 50%;
  padding-inline-start: 0;

}

.fade-enter-active { 
  transition: all .15s;
}

.fade-leave-active {
  transition: all .15s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}

.slide-enter, .slide-leave-active {
  height: 0;
}

.slide-enter-active { 
  transition: all 0.5s ease;
}

.slide-leave-active {
  transition: all 0.5s ease;
}

</style>

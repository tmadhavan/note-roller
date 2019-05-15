<template>
  <div class="optionsContainer">
    <div class="selectNotesContainer">
      <div>Notes</div>
      <ul>
        <li v-for="note in notes" :key="note">
          <input type="checkbox" :id="note" :value="note" v-model="selectedNotes">
          <label :for="note" > {{ note }}</label>
        </li>
      </ul>
    </div>
    <div class="selectDirectionsContainer">
      <div>Directions</div>
      <ul>
        <v-c
        <li v-for="direction in directions" :key="direction">
          <input
            type="checkbox"
            :id="direction"
            :value="direction"
            v-model="selectedDirections"
          >
          <label :for="direction"> {{ direction }}</label>
        </li>
      </ul>
    </div>
    <div class="clickOptionsContainer"></div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

/**
 * A component that displays and tracks the notes and directions that are selected by a user. The roller component
 * only takes its displayed notes/directions from the set that the user has selected.
 */
@Component
export default class OptionBar extends Vue {
  @Prop(Array) notes!: Array<string>;
  @Prop(Array) directions!: Array<string>;

  selectedNotes: string[] = new Array<string>();
  selectedDirections: string[] = new Array<string>();
}
</script>

<style lang="scss" scoped>
.optionsContainer {
  height: 100px;

  & li {
    display: inline-block;
    padding-left: 0.8em;
  }
}

.selectNotesContainer {
  height: 100px;
}

.selectDirectionsContainer {
  height: 100px;
}
</style>

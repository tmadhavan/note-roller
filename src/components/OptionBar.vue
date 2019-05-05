<template>
    <div class="optionsContainer">
        <div class="selectNotesContainer">
            <div> Notes </div>
            <ul>
                <li v-for="note in notes.values()"> {{ note }}
                    <input type="checkbox" :id="note" :value="note" v-model="selectedNotes">
<!--                    <label :for="note" > {{ note }}</label>-->
                </li>
            </ul>
        </div>
        <div class="selectDirectionsContainer">
            <div> Directions </div>
            <ul>
                <li v-for="direction in directions.values()"> {{ direction }}
                    <input type="checkbox" :id="direction" :value="direction" v-model="selectedDirections">
<!--                <label :for="direction"> {{ direction }}</label>-->
                </li>
            </ul>
        </div>
        <div class="clickOptionsContainer">

        </div>
    </div>
</template>

<script lang="ts">
  import { Component, Prop, Vue } from 'vue-property-decorator';

  @Component
  export default class OptionBar extends Vue {

    @Prop(Map) notes!: Map<number, string>;
    @Prop(Map) directions!: Map<number, string>;

    selectedNotes: string[] = new Array<string>();
    selectedDirections: string[] = new Array<string>();

  }
</script>

<style lang="scss" scoped>
    .optionsContainer {
        height: 100px;

        & li {
            display: inline-block;
        }
    }

    .selectNotesContainer {
        height: 100px;
    }

    .selectDirectionsContainer {
        height: 100px;
    }
</style>

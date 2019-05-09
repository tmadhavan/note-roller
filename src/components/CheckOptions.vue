<template>
  <div class="optionsContainer">
      <div> {{ optionName }} </div>
      <ul>
        <li v-for="option in options" :key="option">
          <input type="checkbox" :id="option" :value="option" v-model="selectedOptions">
          <label :for="option" > {{ option }}</label>
        </li>
      </ul>   
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Model, Watch} from "vue-property-decorator";

@Component
export default class CheckOptions extends Vue {
  @Prop(String) optionName!: string;
  @Prop(Array) options!: Array<string>; 

  @Watch('selectedOptions')
  onSelectedOptionsChanged(val: string[], oldVal: string[]) {
    this.$emit('optionChanged', this.selectedOptions);
  }

  selectedOptions: string[] = new Array<string>();
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
</style>

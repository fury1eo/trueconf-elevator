<template>
  <HouseWrapper :levelsCount="levelsCount" :elevatorsCount="elevatorsCount"/>
</template>

<script>
  import HouseWrapper from './components/HouseWrapper';

  export default {
    components: { HouseWrapper },
    data() {
      return {
        // Set your values here
        elevatorsCount: 1, // Elevators count
        levelsCount: 5 // Floors count
      }
    }
  }
</script>

<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: Avenir, Helvetica, Arial, sans-serif;
    color: #2c3e50;
  }
  #app {
    padding: 0 10px;
  }
</style>

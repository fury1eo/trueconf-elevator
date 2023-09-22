<template>

  <HouseWrapper :levelsCount="levelsCount" :liftsCount="liftsCount"/>

</template>

<script>

  import HouseWrapper from './components/HouseWrapper';

  export default {

    components: { HouseWrapper },

    data() {
      return {
        liftsCount: 2,
        levelsCount: 5
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

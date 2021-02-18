<template>
  <div>
    <div class="vampire-dice-symbols">
      <div class="vampire-die-column"/>

      <div class="vampire-die-column">
        <img src="../img/vampire-black.png" class="die-icon"/>
        <div class="die-text">
          <p>Normal Dice</p>
          <input type="number" min="0" max="99" v-model="nBlack"/>
        </div>
      </div>

      <div class="vampire-die-column">
        <img src="../img/vampire-red.png" class="die-icon"/>
        <div class="die-text">
          <p>Hunger Dice</p>
          <input type="number" min="0" max="99" v-model="nRed"/>
        </div>
      </div>

      <div class="vampire-die-column"/>
    </div>

    <button @click="roll">Roll!</button>
  </div>
</template>

<script>
  export default {
    name: "VampireRoller",
    data () {
      return {
        nRed: 0,
        nBlack: 0,
      }
    },
    methods: {
      roll: function () {
        this.$emit('event:Roll', this.nBlack, this.nRed);
      }
    }
  }
</script>

<style scoped>

  .die-icon {
    flex: 4;
  }

  .die-text {
    flex: 1;
  }

  .vampire-dice-symbols {
    display: flex;
    flex-direction: row;
  }

  .vampire-die-column {
    flex: 1;
    display: flex;
    flex-direction: column;
    width: 128px;
  }
</style>

<template>
  <div>
    <div class="vampire-dice-symbols">
      <div class="vampire-die-column"/>

      <div class="vampire-die-column">
        <div class="die-icon normal"/>
        <div>
          <p>Normal Dice</p>
          <input type="number" min="0" max="99" v-model="nBlack"/>
        </div>
      </div>

      <div class="vampire-die-column">
        <div class="die-icon hunger"/>
        <div>
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
    height: 128px;
    background-repeat: no-repeat;
    background-position: center;
    padding-left: 120px;
  }

  .normal {
    background-image: url("../img/vampire-black.png");
  }

  .hunger {
    background-image: url("../img/vampire-red.png");
  }

  .vampire-dice-symbols {
    display: flex;
    flex-direction: row;
  }

  .vampire-die-column {
    flex: 1;
  }

  input {
    width: 33%;
  }

  * {
    text-align: center;
  }
</style>

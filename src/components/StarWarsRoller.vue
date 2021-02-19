<template>
  <div>
    <div class="dice-row">
      <div class="dice-col">
        <div class="die-icon boost"/>
        <p>Boost Dice</p>
        <input type="number" min="0" max="99" v-model="nBlue">
      </div>

      <div class="dice-col">
        <div class="die-icon ability"/>
        <p>Ability Dice</p>
        <input type="number" min="0" max="99" v-model="nGreen">
      </div>

      <div class="dice-col">
        <div class="die-icon proficiency"/>
        <p>Proficiency Dice</p>
        <input type="number" min="0" max="99" v-model="nYellow">
      </div>
    </div>

    <div class="dice-row">
      <div class="dice-col">
        <div class="die-icon setback"/>
        <p>Setback Dice</p>
        <input type="number" min="0" max="99" v-model="nBlack">
      </div>

      <div class="dice-col">
        <div class="die-icon difficulty"/>
        <p>Difficulty Dice</p>
        <input type="number" min="0" max="99" v-model="nPurple">
      </div>

      <div class="dice-col">
        <div class="die-icon challenge"/>
        <p>Challenge Dice</p>
        <input type="number" min="0" max="99" v-model="nRed">
      </div>
    </div>

    <button @click="standardRoll">Roll!</button>

    <hr>

    <div class="dice-col">
      <div class="die-icon force"/>
      <p>Force Dice</p>
      <input type="number" min="0" max="99" v-model="nWhite" class="force-input">
    </div>
    <button @click="forceRoll">Roll!</button>
  </div>
</template>

<script>
  export default {
    name: "StarWarsRoller",
    data () {
      return {
        nBlue: 0,
        nBlack: 0,
        nGreen: 0,
        nPurple: 0,
        nYellow: 0,
        nRed: 0,
        nWhite: 0
      }
    },
    methods: {
      standardRoll: function () {
        this.$emit('event:StandardRoll', this.nBlue, this.nGreen, this.nYellow, this.nBlack, this.nPurple, this.nRed);
      },

      forceRoll: function () {
        this.$emit('event:ForceRoll', this.nWhite);
      }
    }
  }
</script>

<style scoped>
  .dice-row {
    display: flex;
    flex-direction: row;
  }

  .dice-col {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-content: center;
    justify-content: center;
  }

  .die-icon {
    height: 128px;
    background-repeat: no-repeat;
    background-position: center;
    margin-top: 20px;
  }

  .boost {
    background-image: url("../img/starwars-blue.png");
  }

  .setback {
    background-image: url("../img/starwars-black.png");
  }

  .ability {
    background-image: url("../img/starwars-green.png");
  }

  .difficulty {
    background-image: url("../img/starwars-purple.png");
  }

  .proficiency {
    background-image: url("../img/starwars-yellow.png");
  }

  .challenge {
    background-image: url("../img/starwars-red.png");
  }

  .force {
    background-image: url("../img/starwars-white.png");
  }

  input {
    width: 33%;
    margin-right: auto;
    margin-left: auto;
  }

  button {
    margin-top: 20px;
    margin-bottom: 20px;
  }

  * {
    text-align: center;
  }

  .force-input {
    width: 12%;
  }
</style>

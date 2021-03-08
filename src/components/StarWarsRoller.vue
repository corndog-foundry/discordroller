<template>
    <div>
      <label>
        <select v-model="rollType" class="type-selector">
          <option v-for="type in rollTypes" :key="type">{{ type }}</option>
        </select>
      </label>

      <div v-if="rollType === 'Standard'">
        <div class="roll-text" @click="standardRoll">
          <p>{{ rollFormula }}</p>
        </div>

        <div class="die-row">
          <div/>
          <div>
            <button class="boost" @click="bumpBlue"/>
            <p v-if="rollLabels">Boost</p>
          </div>

          <div>
            <button class="ability" @click="bumpGreen"/>
            <p v-if="rollLabels">Ability</p>
          </div>

          <div>
            <button class="proficiency" @click="bumpYellow"/>
            <p v-if="rollLabels">Proficiency</p>
          </div>
          <div/>
        </div>

        <div class="die-row">
          <div/>

          <div>
            <button class="setback" @click="bumpBlack"/>
            <p v-if="rollLabels">Setback</p>
          </div>

          <div>
            <button class="difficulty" @click="bumpPurple"/>
            <p v-if="rollLabels">Difficulty</p>
          </div>

          <div>
            <button class="challenge" @click="bumpRed"/>
            <p v-if="rollLabels">Challenge</p>
          </div>
          <div/>
        </div>
      </div>
      <div v-else>
        <div class="roll-text" @click="forceRoll">
          <p v-if="nWhite > 0">{{ nWhite }} force</p>
        </div>

        <div>
          <button class="force" @click="bumpWhite"/>
          <p v-if="rollLabels">Force</p>
        </div>
      </div>
    </div>
</template>

<script>
  export default {
    name: "StarWarsRoller",
    props: ['rollLabels'],
    data () {
      return {
        nBlue: 0,
        nBlack: 0,
        nGreen: 0,
        nPurple: 0,
        nYellow: 0,
        nRed: 0,
        nWhite: 0,
        rollFormula: '',
        rollType: 'Standard',
        rollTypes: [
            'Standard',
            'Force'
        ]
      }
    },
    methods: {
      calcRollFormula: function () {
        let formula = '';

        if (this.nBlue > 0) formula += `${this.nBlue} boost`
        if (this.nBlack > 0) {
          if (formula === '') {
            formula += `${this.nBlack} setback`
          } else {
            formula += ` + ${this.nBlack} setback`
          }
        }
        if (this.nGreen > 0) {
          if (formula === '') {
            formula += `${this.nGreen} ability`
          } else {
            formula += ` + ${this.nGreen} ability`
          }
        }
        if (this.nPurple > 0) {
          if (formula === '') {
            formula += `${this.nPurple} difficulty`
          } else {
            formula += ` + ${this.nPurple} difficulty`
          }
        }
        if (this.nYellow > 0) {
          if (formula === '') {
            formula += `${this.nYellow} proficiency`
          } else {
            formula += ` + ${this.nYellow} proficiency`
          }
        }
        if (this.nRed > 0) {
          if (formula === '') {
            formula += `${this.nRed} challenge`
          } else {
            formula += ` + ${this.nRed} challenge`
          }
        }

        this.rollFormula = formula;
      },
      bumpBlue: function () {
        this.nBlue++;
        this.calcRollFormula();
      },
      bumpBlack: function () {
        this.nBlack++;
        this.calcRollFormula();
      },
      bumpGreen: function () {
        this.nGreen++;
        this.calcRollFormula();
      },
      bumpPurple: function () {
        this.nPurple++;
        this.calcRollFormula();
      },
      bumpYellow: function () {
        this.nYellow++;
        this.calcRollFormula();
      },
      bumpRed: function () {
        this.nRed++;
        this.calcRollFormula();
      },
      bumpWhite: function () {
        this.nWhite++;
      },
      standardRoll: function () {
        this.$emit('event:StandardRoll', this.nBlue, this.nGreen, this.nYellow, this.nBlack, this.nPurple, this.nRed, this.rollFormula);
        this.reset();
      },

      forceRoll: function () {
        this.$emit('event:ForceRoll', this.nWhite);
        this.nWhite = 0;
      },

      reset: function () {
        this.nBlue = 0;
        this.nBlack = 0;
        this.nGreen = 0;
        this.nPurple = 0;
        this.nYellow = 0;
        this.nRed = 0;
        this.rollFormula = '';
      }
    }
  }
</script>

<style scoped>
  .boost {
    background-image: url("../img/starwars-blue.png");
  }

  .setback {
    background-image: url("../img/starwars-black.png");
  }

  .boost:hover,
  .setback:hover {
    background-image: url("../img/starwars-blbr-selected.png");
  }

  .ability {
    background-image: url("../img/starwars-green.png");
  }

  .difficulty {
    background-image: url("../img/starwars-purple.png");
  }

  .ability:hover,
  .difficulty:hover {
    background-image: url("../img/starwars-gp-selected.png");
  }

  .proficiency {
    background-image: url("../img/starwars-yellow.png");
  }

  .challenge {
    background-image: url("../img/starwars-red.png");
  }

  .proficiency:hover,
  .challenge:hover {
    background-image: url("../img/starwars-yr-selected.png");
  }

  .force {
    background-image: url("../img/starwars-white.png");
  }

  button {
    height: 128px;
    width: 128px;
    border: none;
  }

  .die-row {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    margin-bottom: 20px;
  }

  .roll-text {
    background-color: #0c1e30;
    border: 1px solid #0c0e10;
    margin-bottom: 20px;
    height: 50px;
  }

  .roll-text:hover {
    background-color: #FFFFFF;
    color: #000000;
  }

  p {
    background-color: inherit;
    color: inherit;
  }

  .type-selector {
    margin-bottom: 10px;
  }
</style>

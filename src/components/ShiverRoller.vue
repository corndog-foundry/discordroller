<template>
  <div>
    <div class="roll-text" @click="roll">
      <p>{{ rollFormula }}</p>
    </div>

    <div class="die-row">
      <div/>
      <div>
        <button class="skill" @click="bumpSkill"/>
        <p v-if="rollLabels">Skill</p>
      </div>
      <div/>
    </div>

    <div class="die-row">
      <div/>
      <div>
        <button class="talent" @click="bumpTalent"/>
        <p v-if="rollLabels">Talent</p>
      </div>
      <div/>
    </div>
  </div>
</template>

<script>
export default {
  name: "ShiverRoller",
  props: ['rollLabels'],
  data () {
    return {
      rollFormula: '',
      nSkill: 0,
      nTalent: 0
    }
  },
  methods: {
    bumpSkill: function () {
      this.nSkill++;
      this.calcRollFormula();
    },
    bumpTalent: function () {
      this.nTalent++;
      this.calcRollFormula();
    },
    calcRollFormula: function () {
      let formula = '';

      if (this.nSkill > 0) formula += `${this.nSkill} skill`
      if (this.nTalent > 0) {
        if (formula === '') {
          formula += `${this.nTalent} talent`
        } else {
          formula += ` + ${this.nTalent} talent`
        }
      }

      this.rollFormula = formula;
    },
    roll: function () {
      this.$emit('event:Roll', this.nSkill, this.nTalent, this.rollFormula);
      this.reset();
    },
    reset: function () {
      this.nSkill = 0;
      this.nTalent = 0;
      this.rollFormula = '';
    }
  }
}
</script>

<style scoped>
  .skill {
    background-image: url("../img/shiver-skill.png");
  }

  .talent {
    background-image: url("../img/shiver-talent.png");
  }

  .skill:hover {
    background-image: url("../img/starwars-blbr-selected.png");
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
</style>

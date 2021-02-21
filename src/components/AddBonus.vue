<template>
  <div>
    <p>What bonus would you like to add?</p>
    <input v-model="bonus" type="number">
    <button @click="setBonus">Add Bonus</button>
    <button @click="cancel">Cancel</button>
  </div>
</template>

<script>
  export default {
    name: "AddBonus",
    data () {
      return {
        bonus: 0,
      }
    },
    methods: {
      setBonus: function () {
        this.$emit('set:Bonus', this.bonus);
      },
      cancel: function () {
        this.$emit('cancel:SetBonus');
      }
    }
  }
</script>

<style scoped>
  .div {
    background-color: #42b983;
    color: #0c1e30;
  }
</style>

<template>
  <div>
    <form @submit.prevent="submitKey">
      <p>Enter your <b>Webhook URL</b> here:</p>
      <input type="text" v-model="newKey" required/>

      <p>Enter a <b>nickname</b> here:</p>
      <input type="text" v-model="newNick" required/>

      <p>Verbose mode? (Display the result of all rolls)</p>
      <input type="checkbox" v-model="newVerboseMode"/>

      <p>Add dice labels?</p>
      <input type="checkbox" v-model="newRollLabels"/>

      <div></div>

      <button>Start</button>
    </form>
  </div>
</template>

<script>
  export default {
    name: "GetKey",
    data () {
      return {
        newKey: '',
        newNick: '',
        newVerboseMode: false,
        newRollLabels: false
      }
    },
    methods: {
      submitKey: function () {
        this.$emit('change:DiscordKey', this.newKey, this.newNick, this.newVerboseMode, this.newRollLabels);
      }
    }
  }
</script>

<style scoped>
  button {
    margin-top: 25px;
  }

  table {
    justify-content: center;
    align-content: center;
  }
</style>

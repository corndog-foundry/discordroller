<template>
  <div>
    <form @submit.prevent="submitKey">
      <p>Enter your Webhook URL here:</p>
      <input type="text" v-model="newKey" required/>

      <p>Enter a nickname here:</p>
      <input type="text" v-model="newNick" required/>

      <p>Verbose mode? (Display the result of all rolls)</p>
      <input type="checkbox" v-model="newVerboseMode"/>

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
      }
    },
    methods: {
      submitKey: function () {
        this.$emit('change:DiscordKey', this.newKey, this.newNick, this.newVerboseMode);
      }
    }
  }
</script>

<style scoped>
  button {
    margin-top: 25px;
  }
</style>

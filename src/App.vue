<template>
  <div id="app">
    <h2>DOM-Based XSS Demo</h2>

    <input v-model="userInput" placeholder="Type something" />

    <button @click="inject">Inject into page</button>

    <h3>Output Area (VULNERABLE)</h3>
    <div ref="outputBox" class="box"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userInput: ""
    }
  },
  methods: {
    inject() {
      this.$refs.outputBox.innerHTML = this.userInput
    }
  }
}
</script>

<style>
.box {
  border: 1px solid #444;
  padding: 15px;
  margin-top: 10px;
  min-height: 40px;
}
</style>

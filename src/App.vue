<template>
  <div>
    <div>{{ firstNumber }}</div>
    <div>
      <button @click="press('1')">1</button>
      <button @click="press('2')">2</button>
      <button @click="press('3')">3</button>
    </div>
    <div>
      <button @click="press('4')">4</button>
      <button @click="press('5')">5</button>
      <button @click="press('6')">6</button>
    </div>
    <div>
      <button @click="press('7')">7</button>
      <button @click="press('8')">8</button>
      <button @click="press('9')">9</button>
    </div>
    <div>
      <button @click="press('.')">.</button>
      <button @click="press('0')">0</button>
      <button @click="press('C')">C</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      firstNumber: ''
    }
  },
  methods: {
    press (a) {
      this.firstNumber += a
    }
  }
}
</script>

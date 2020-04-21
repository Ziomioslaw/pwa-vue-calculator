<template>
  <div>
    <div>{{ display }}</div>
    <div>
      <button @click="pressedDigit('1')">1</button>
      <button @click="pressedDigit('2')">2</button>
      <button @click="pressedDigit('3')">3</button>
    </div>
    <div>
      <button @click="pressedDigit('4')">4</button>
      <button @click="pressedDigit('5')">5</button>
      <button @click="pressedDigit('6')">6</button>
    </div>
    <div>
      <button @click="pressedDigit('7')">7</button>
      <button @click="pressedDigit('8')">8</button>
      <button @click="pressedDigit('9')">9</button>
    </div>
    <div>
      <button @click="pressedDot('.')">.</button>
      <button @click="pressedDigit('0')">0</button>
      <button @click="pressedClear()">C</button>
    </div>
    <div>
      <button @click="pressedOperation('+')">+</button>
      <button @click="pressedOperation('-')">-</button>
      <button @click="pressedOperation('*')">*</button>
      <button @click="pressedOperation('/')">/</button>
    </div>
    <button @click="pressedCalculate('=')">=</button>
    <button @click="pressedBackspace()">back</button>
  </div>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      currentResult: 0,
      currentOperation: '+',
      currentNumber: '',
      display: '0'
    }
  },
  methods: {
    calculate () {
      const value = +this.currentNumber
      this.currentNumber = ''

      switch (this.currentOperation) {
        case '+':
          this.currentResult += value
          break

        case '-':
          this.currentResult -= value
          break

        case '/':
          if (value === 0) {
            this.pressedClear()
            this.display = 'div by zero'
            return
          }

          this.currentResult /= value
          break

        case '*':
          this.currentResult *= value
          break
      }
    },
    pressedOperation (operation) {
      this.calculate()
      this.currentOperation = operation
      this.display = this.currentResult
    },
    pressedCalculate () {
      this.calculate()
      this.display = this.currentResult
    },
    pressedClear () {
      this.display = '0'
      this.currentResult = 0
      this.currentOperation = '+'
      this.currentNumber = ''
    },
    pressedBackspace () {
      const len = this.currentNumber.length

      if (len > 1) {
        this.currentNumber = this.currentNumber.substring(0, len - 1)
      } else if (len <= 1) {
        this.currentNumber = '0'
      }

      this.display = this.currentNumber
    },
    pressedDigit (character) {
      if ((this.currentNumber === '0') || (character === '0' && this.currentNumber === '0')) {
        this.currentNumber = character
      } else {
        this.currentNumber += character
      }

      this.display = this.currentNumber
    },
    pressedDot () {
      if (this.currentNumber === '') {
        this.pressedDigit('0')
        this.pressedDigit('.')
        return
      }

      if (this.currentNumber.indexOf('.') > -1) {
        return
      }

      this.pressedDigit('.')
    }
  }
}
</script>

<template>
  <div class="root">
    <div class="display">{{ display }}</div>
    <div class="numberpad">
      <div>
        <button @click="pressedDigit('1')">1</button>
        <button @click="pressedDigit('2')">2</button>
        <button @click="pressedDigit('3')">3</button>
        <button @click="pressedClear()">C</button>
      </div>
      <div>
        <button @click="pressedDigit('4')">4</button>
        <button @click="pressedDigit('5')">5</button>
        <button @click="pressedDigit('6')">6</button>
        <button @click="pressedOperation('-')">-</button>
      </div>
      <div>
        <button @click="pressedDigit('7')">7</button>
        <button @click="pressedDigit('8')">8</button>
        <button @click="pressedDigit('9')">9</button>
        <button @click="pressedOperation('+')">+</button>
      </div>
      <div>
        <button @click="pressedDot()">.</button>
        <button @click="pressedDigit('0')">0</button>
        <button @click="pressedOperation('/')">/</button>
        <button @click="pressedOperation('*')">*</button>
      </div>
      <div>
        <button @click="pressedCalculate('=')">=</button>
        <button @click="pressedBackspace()">back</button>
      </div>
    </div>
  </div>
</template>

<script>
const calculate = function (firstNumber, secondNumber, currentOperation) {
  switch (currentOperation) {
    case '+':
      return firstNumber + secondNumber

    case '-':
      return firstNumber - secondNumber

    case '/':
      if (secondNumber === 0) {
        throw new Error('div by zero')
      }

      return firstNumber / secondNumber

    case '*':
      return firstNumber * secondNumber
  }

  throw new Error('Unknown operation')
}

const FirstNumberMode = function (firstNumber, secondNumber, currentOperation) {
  console.log('FirstNumberMode', firstNumber, secondNumber, currentOperation)
  return {
    introduceNumber: function (newValue) {
      return new FirstNumberMode(newValue, null, null)
    },
    pressedOperation: function (operation) {
      return new SelectOperationMode(firstNumber, null, operation)
    },
    pressedCalculate: function () {
      return this
    },
    getDisplay: function () {
      return firstNumber
    }
  }
}

const SelectOperationMode = function (firstNumber, secondNumber, currentOperation) {
  console.log('SelectOperationMode', firstNumber, secondNumber, currentOperation)
  return {
    introduceNumber: function (newValue) {
      return new SecondNumberMode(firstNumber, newValue, currentOperation)
    },
    pressedOperation: function (operation) {
      return new SelectOperationMode(firstNumber, secondNumber, operation)
    },
    pressedCalculate: function () {
      return this
    },
    getDisplay: function () {
      return firstNumber
    }
  }
}

const SecondNumberMode = function (firstNumber, secondNumber, currentOperation) {
  console.log('SecondNumberMode', firstNumber, secondNumber, currentOperation)
  return {
    introduceNumber: function (newValue) {
      return new SecondNumberMode(firstNumber, newValue, currentOperation)
    },
    pressedOperation: function (operation) {
      try {
        return new SecondNumberMode(calculate(firstNumber, secondNumber, currentOperation), null, operation)
      } catch (exception) {
        return new ErrorMode(exception.message, null, null)
      }
    },
    pressedCalculate: function () {
      try {
        return new ResultMode(calculate(firstNumber, secondNumber, currentOperation), null, null)
      } catch (exception) {
        return new ErrorMode(exception.message, null, null)
      }
    },
    getDisplay: function () {
      return secondNumber
    }
  }
}

const ResultMode = function (firstNumber, secondNumber, currentOperation) {
  console.log('ResultMode', firstNumber, secondNumber, currentOperation)
  return {
    introduceNumber: function (newValue) {
      return new FirstNumberMode(newValue, null, null)
    },
    pressedOperation: function (operation) {
      return new SecondNumberMode(firstNumber, null, operation)
    },
    pressedCalculate: function () {
      return this
    },
    getDisplay: function () {
      return firstNumber
    }
  }
}

const ErrorMode = function (firstNumber, secondNumber, currentOperation) {
  console.log('ErrorMode', firstNumber, secondNumber, currentOperation)
  return {
    introduceNumber: function (newValue) {
      return new FirstNumberMode(newValue, null, null)
    },
    pressedOperation: function (operation) {
      return this
    },
    pressedCalculate: function () {
      return this
    },
    getDisplay: function () {
      return firstNumber
    }
  }
}

export default {
  name: 'App',
  data: function () {
    return {
      mode: new FirstNumberMode(0, null, null),
      currentInput: '0',
      display: '0'
    }
  },
  methods: {
    pressedDigit: function (character) {
      if (this.currentInput === '0') {
        this.currentInput = character
      } else {
        this.currentInput += character
      }

      this.mode = this.mode.introduceNumber(+this.currentInput)
      this.display = this.currentInput
    },

    pressedDot: function () {
      if (this.currentInput === '') {
        this.currentInput = '0.'
        return
      }

      if (this.currentInput.indexOf('.') > -1) {
        return
      }

      this.currentInput += '.'
      this.mode = this.mode.introduceNumber(+this.currentInput)
      this.display = this.currentInput
    },

    pressedClear: function () {
      this.mode = new FirstNumberMode(0, null, null)
      this.currentInput = '0'
      this.display = this.currentInput
    },

    pressedBackspace: function () {
      const len = this.currentInput.length

      if (len > 1) {
        this.currentInput = this.currentInput.substring(0, len - 1)
      } else if (len <= 1) {
        this.currentInput = '0'
      }

      this.mode = this.mode.introduceNumber(+this.currentInput)
      this.display = this.currentInput
    },

    pressedCalculate: function () {
      this.currentInput = '0'
      this.mode = this.mode.pressedCalculate()
      this.display = this.mode.getDisplay()
    },

    pressedOperation (operation) {
      this.mode = this.mode.pressedOperation(operation)
      this.currentInput = '0'
    }
  }
}
</script>

<style>
html {
  height: 100%;
  padding: 0;
  margin: 0;
}
body {
  height: 100%;
  padding: 0;
  margin: 0;
  font-family: Verdana, Geneva, Tahoma, sans-serif;
}
</style>

<style scoped>
div.root {
  height: 100%;
}

div.display {
  padding: 0;
  margin: 0;
  font-size: 2.4em;
  text-align: right;
  height: 11%;
}

div.numberpad {
  position: relative;
  width: 100%;
  height: 89%;
}

div.numberpad > div {
  height: 20%;
}

button {
  width: 25%;
  height: 100%;
  padding: 0;
  margin: 0;
  background-color: white;
  border: 1px solid black;
  font-family: inherit;
  font-size: 100%;
  line-height: 1.15;
  overflow: visible;
  text-transform: none;
  -webkit-appearance: button;
}

button {
  outline: none !important;
}

input[type="button"]::-moz-focus-inner {
  border: 0;
}

div.numberpad > div:last-child button {
  width: 50%;
}
</style>

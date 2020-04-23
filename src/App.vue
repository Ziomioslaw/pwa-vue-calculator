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
const calculate = function (firstNumber, secondNumber, currentOperation) {
  switch (currentOperation) {
    case '+':
      return firstNumber + secondNumber

    case '-':
      return firstNumber - secondNumber

    case '/':
      if (secondNumber === 0) {
        return 'div by zero'
      }

      return firstNumber / secondNumber

    case '*':
      return firstNumber * secondNumber
  }

  return 'Unknown operation'
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
      return new SecondNumberMode(calculate(firstNumber, secondNumber, currentOperation), null, operation)
    },
    pressedCalculate: function () {
      return new ResultMode(calculate(firstNumber, secondNumber, currentOperation), null, null)
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
      this.mode = this.mode.pressedCalculate()
      this.currentInput = '0'
      this.display = this.mode.getDisplay()
    },

    pressedOperation (operation) {
      this.mode = this.mode.pressedOperation(operation)
      this.currentInput = '0'
      this.display = '0'
    }
  }
}
</script>

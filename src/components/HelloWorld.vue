<template>
  <div class="root">
    <h1>XM's calculator</h1>
    <div class="calculator">
      <div class="result" style="grid-area: result">
        <span>{{equation}}</span>
      </div>

      <button style="grid-area: ac" @click="clear">AC</button>
      <button style="grid-area: plus-minus" @click="calculateToggle">±</button>
      <button style="grid-area: percent" @click="calculatePercentage">%</button>
      <button style="grid-area: add" @click="append('+')">+</button>
      <button style="grid-area: subtract" @click="append('-')">-</button>
      <button style="grid-area: multiply" @click="append('×')">×</button>
      <button style="grid-area: divide" @click="append('÷')">÷</button>
      <button style="grid-area: equal" @click="calculate">=</button>

      <button style="grid-area: number-1" @click="append(1)">1</button>
      <button style="grid-area: number-2" @click="append(2)">2</button>
      <button style="grid-area: number-3" @click="append(3)">3</button>
      <button style="grid-area: number-4" @click="append(4)">4</button>
      <button style="grid-area: number-5" @click="append(5)">5</button>
      <button style="grid-area: number-6" @click="append(6)">6</button>
      <button style="grid-area: number-7" @click="append(7)">7</button>
      <button style="grid-area: number-8" @click="append(8)">8</button>
      <button style="grid-area: number-9" @click="append(9)">9</button>
      <button style="grid-area: number-0" @click="append(0)">0</button>

      <button style="grid-area: dot" @click="append('.')">.</button>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      equation: '0',
      isDecimalAdded: false, // 判断是否输入了小数点
      isOperatorAdded: false, // 判断是否点击了 "+" "-" "*" "/"
      isStarted: false, // 判断是否输入数字

      isEntering: false
    }
  },
  methods: {
    // Check if the character is + / - / × / ÷ 判断点击符号
    isOperator (character) {
      return ['+', '-', '×', '÷'].indexOf(character) > -1
    },
    // when pressed operators or numbers
    append (character) {
      if (this.equation === '0' && !this.isOperator(character)) {
        if (character === '.') {
          this.equation += '' + character
          this.isDecimalAdded = true
          this.isOperatorAdded = true
        } else {
          this.equation = '' + character

          this.isEntering = true
        }

        this.isStarted = true
        return
      }

      // If Number
      if (!this.isOperator(character)) {
        if (character === '.' && this.isDecimalAdded) {
          return
        }

        if (character === '.') {
          this.isDecimalAdded = true
          // 不可以直接输入运算符
          this.isOperatorAdded = true
        } else {
          this.isOperatorAdded = false
        }

        if (!this.isEntering) {
          this.isEntering = true
          this.equation = '' + character
          return
        }

        this.equation += '' + character
      }

      // Added pressed   判断输入的是运算符
      if (this.isOperator(character) && !this.isOperatorAdded) {
        this.equation += '' + character
        this.isDecimalAdded = false
        this.isOperatorAdded = true

        this.isEntering = true
      }
    },
    // when pressed "="
    calculate () {
      let result = this.equation.replace(new RegExp('×', 'g'), '*').replace(new RegExp('÷', 'g'), '/')

      // eslint-disable-next-line no-eval
      let ans = eval(result)

      this.equation = (ans < 1.0e9 ? parseFloat(ans.toFixed(9)) : ans.toExponential(3)).toString()

      this.isDecimalAdded = false
      this.isOperatorAdded = false

      this.isEntering = false
    },
    // when pressed "±"
    calculateToggle () {
      if (this.isOperatorAdded || !this.isStarted) {
        return
      }
      this.equation = this.equation + '* -1'
      this.calculate()
    },
    // when pressed "%"
    calculatePercentage () {
      if (this.isOperatorAdded || !this.isStarted) {
        return
      }
      this.equation = this.equation + '* 0.01'
      this.calculate()
    },
    // when pressed 'AC'
    clear () {
      this.equation = '0'
      this.isDecimalAdded = false
      this.isOperatorAdded = false
      this.isStarted = false

      this.isEntering = false
    }
  }
}
</script>
<style scoped>
.root h1 {
  text-align: center;
  color: #999;
}
.calculator {
  --button-width: 80px;
  --button-height: 80px;

  display: grid;
  grid-template-areas: "result result result result"
    "ac plus-minus percent divide"
    "number-7 number-8 number-9 multiply"
    "number-4 number-5 number-6 subtract"
    "number-1 number-2 number-3 add"
    "number-0 number-0 dot equal";
  /* 设置横向列 */
  grid-template-columns: repeat(4, var(--button-width));
  grid-template-rows: repeat(6, var(--button-height));

  box-shadow: -8px -8px 16px 10px rgba(255, 255, 255, 1),8px 8px 16px -10px rgba(0, 0, 0, .15);
  padding: 24px;
  border-radius: 20px;
}

.calculator button {
  margin: 8px;
  padding: 0;
  border: 0;
  display: block;
  outline: none;
  border-radius: calc(var(--button-height) / 2);
  font-size: 24px;
  font-family: Helvetica;
  color: #999;
  background: linear-gradient(135deg, rgba(230,230,230,1) 0%,rgba(246,246,246,1)100%);
  box-shadow: -4px -4px 10px -8px rgba(255, 255, 255, 1), 4px 4px 10px -8px rgba(0, 0, 0, .3);
}

.calculator button:active {
  box-shadow: -4px -4px 10px -8px rgba(255, 255, 255, 1) inset, 4px 4px 10px -8px rgba(0, 0, 0, .3) inset;
}

.result {
  text-align: right;
  line-height: var(--button-height);
  font-size: 48px;
  font-family: Helvetica;
  padding: 0 20px;
  color: #666;
  overflow: auto;
  -ms-overflow-style: none; /* IE 10+ */
  scrollbar-width: none; /* Firefox */
  display: flex;
  align-items: center;
  justify-content: flex-end;
}
.result span {
  display: inline-block;
  height: 100%;
}

.result::-webkit-scrollbar {
  display: none;
}
</style>

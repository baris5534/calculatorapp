<template>
  <div class="calculator-container bg-blue-950">
    <!-- Hesap makinesi ana container -->
    <div class="calculator">
      <!-- Ekran -->
      <div class="display">{{ display || '0' }}</div>
      
      <!-- Tuş takımı -->
      <div class="keypad">
        <button v-for="button in buttons" 
                :key="button"
                @click="handleClick(button)"
                :class="getButtonClass(button)">
          {{ button }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CalculatorApp',
  data() {
    return {
      display: '', // Ekranda gösterilen değer
      currentNumber: '', // Mevcut girilen sayı
      previousNumber: '', // Önceki girilen sayı
      operation: null, // Seçilen operatör
      expressionDisplay: '', // İfadeyi göstermek için yeni değişken
      buttons: ['C', '±', '%', '÷', '7', '8', '9', '×', '4', '5', '6', '-', '1', '2', '3', '+', '0', '.', '='], // Tuş takımı
      lastClickWasOperation: false // Son tıklanan tuşun operatör olup olmadığını takip eder
    }
  },
  methods: {
    handleClick(value) {
      // Sayı girişi
      if (!isNaN(value) || value === '.') {
        this.handleNumber(value)
      }
      // İşlem tuşları
      else if (['+', '-', '×', '÷'].includes(value)) {
        this.handleOperation(value)
      }
      // Özel işlemler
      else {
        switch(value) {
          case 'C':
            this.clear()
            break
          case '±':
            this.toggleSign()
            break
          case '%':
            this.percentage()
            break
          case '=':
            this.calculate()
            break
        }
      }
    },

    handleNumber(value) {
      // Son tıklama operatör ise yeni sayı girişi için currentNumber'ı sıfırla
      if (this.lastClickWasOperation) {
        this.currentNumber = ''
        this.lastClickWasOperation = false
      }
      
      // Nokta kontrolü
      if (value === '.' && this.currentNumber.includes('.')) return
      
      // Sayıyı ekle ve ekranı güncelle
      this.currentNumber += value
      this.display = this.expressionDisplay + this.currentNumber
    },

    handleOperation(op) {
      if (this.currentNumber) {
        if (this.previousNumber && this.operation) {
          this.calculate()
        } else {
          this.previousNumber = this.currentNumber
          this.operation = op
          this.expressionDisplay = `${this.currentNumber} ${op} `
          this.display = this.expressionDisplay
          this.lastClickWasOperation = true
        }
      }
    },

    calculate() {
      if (!this.previousNumber || !this.currentNumber || !this.operation) return

      const prev = parseFloat(this.previousNumber)
      const current = parseFloat(this.currentNumber)
      let result = 0

      switch(this.operation) {
        case '+':
          result = prev + current
          break
        case '-':
          result = prev - current
          break
        case '×':
          result = prev * current
          break
        case '÷':
          if (current === 0) {
            this.display = 'Hata'
            return
          }
          result = prev / current
          break
      }

      // Sonucu ekrana ve currentNumber'a ata
      this.display = result.toString()
      this.currentNumber = result.toString()
      this.previousNumber = ''
      this.operation = null
      this.expressionDisplay = ''
    },

    clear() {
      this.display = ''
      this.currentNumber = ''
      this.previousNumber = ''
      this.operation = null
      this.expressionDisplay = ''
      this.lastClickWasOperation = false
    },

    toggleSign() {
      if (this.currentNumber) {
        this.currentNumber = (parseFloat(this.currentNumber) * -1).toString()
        this.display = this.currentNumber
      }
    },

    percentage() {
      if (this.currentNumber) {
        this.currentNumber = (parseFloat(this.currentNumber) / 100).toString()
        this.display = this.currentNumber
      }
    },

    getButtonClass(button) {
      return {
        'operator': ['+', '-', '×', '÷'].includes(button),
        'function': ['C', '±', '%'].includes(button),
        'equals': button === '=',
        'number': !isNaN(button) || button === '.'
      }
    }
  }
}
</script>

<style scoped>
.calculator-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: rgb(11, 11, 34);
}

.calculator {
  background: #333;
  border-radius: 10px;
  padding: 20px;
  width: 320px;
}

.display {
  background: #444;
  color: white;
  padding: 20px;
  text-align: right;
  font-size: 2em;
  margin-bottom: 20px;
  border-radius: 5px;
  min-height: 60px;
  word-break: break-all;
}

.keypad {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
}

button {
  padding: 20px;
  font-size: 1.2em;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: 0.2s;
}

button.number {
  background: #666;
  color: white;
}

button.operator {
  background: #ff9500;
  color: white;
}

button.function {
  background: #999;
  color: white;
}

button.equals {
  background: #ff9500;
  color: white;
}

button:hover {
  opacity: 0.8;
}

button:active {
  transform: scale(0.95);
}
</style>
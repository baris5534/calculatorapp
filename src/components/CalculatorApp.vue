<template>
  <div class="calculator-container sm:h-screen sm:bg-[#d9d8e4]">
    <!-- Hesap makinesi ana container -->
    <div class="calculator sm:w-[400px]  h-[800px] max-sm:w-screen max-sm:h-screen flex flex-col justify-end bg-[#212327] sm:rounded-[32px]">
      <!-- Ekran -->
      <div class="display flex flex-col gap-4 p-8">
        <!-- Ana sonuç ekranı -->
        <div class="result text-right">
          {{ display || '0' }}
        </div>
        <!-- Anlık hesaplama sonucu -->
        <div v-if="liveResult !== ''" class="live-result text-gray-500 text-2xl text-right">
          {{ liveResult }}
        </div>
      </div>
      <!-- Tuş takımı -->
      <div class="keypad bg-[#27292E] p-8 rounded-t-3xl">
        <button 
          v-for="button in buttons" 
          :key="button"
          @click="handleClick(button)"
          :class="getButtonClass(button)">
          <template v-if="icons[button]">
            <img :src="icons[button]" :alt="button" class="w-6 h-6">
          </template>
          <template v-else>
            {{ button }}
          </template>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import addIcon from '../assets/icons/Group50.svg'
import subtractIcon from '../assets/icons/Group51.svg'
import multiplyIcon from '../assets/icons/Group52.svg'
import divideIcon from '../assets/icons/Group53.svg'
import percentIcon from '../assets/icons/Group54.svg'
import clearIcon from '../assets/icons/Group55.svg'
import deleteIcon from '../assets/icons/delete.svg'
import zeroIcon from '../assets/icons/Group58.svg'
// Diğer iconlar...

export default {
  name: 'CalculatorApp',
  data() {
    return {
      display: '', // Ekranda gösterilen değer
      currentNumber: '', // Mevcut girilen sayı
      previousNumber: '', // Önceki girilen sayı
      operation: null, // Seçilen operatör
      expression: '', // İşlem geçmişi
      buttons: [ '%', '÷','×','<', '7', '8', '9', '-', '4', '5', '6', '+', '1', '2', '3','⏺️','AC', '0', '.'], // ⏺️ equals butonu için boş yer bırakıldı
      lastClickWasOperation: false, // Son tıklanan tuşun operatör olup olmadığını takip eder
      icons: {
        '+': addIcon,
        '-': subtractIcon,
        '×': multiplyIcon,
        '÷': divideIcon,
        '%': percentIcon,
        '⏺️': clearIcon,
        '⏺': zeroIcon,
        '<': deleteIcon,
        // Diğer iconlar...
      }
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
      // Silme tuşu
      else if (value === '<') {
        this.deleteLastDigit()
      }
      // Özel işlemler
      else {
        switch(value) {
          case 'AC':
            this.clear()
            break
          case '±':
            this.toggleSign()
            break
          case '%':
            this.percentage()
            break
          case '⏺️':
            this.calculate()
            break
        }
      }
    },

    handleNumber(value) {
      // Operatör seçilmişse yeni sayı girişi başlat
      if (this.operation && !this.currentNumber) {
        this.currentNumber = value
      } else {
        // Nokta kontrolü
        if (value === '.' && this.currentNumber.includes('.')) return
        
        // Sayıyı ekle
        if (this.currentNumber === '0' && value !== '.') {
          this.currentNumber = value
        } else {
          this.currentNumber += value
        }
      }
      
      // Expression güncelleme
      this.expression = this.previousNumber ? 
        `${this.previousNumber} ${this.operation} ${this.currentNumber}` : 
        this.currentNumber
      
      this.display = this.expression
    },

    handleOperation(op) {
      if (!this.currentNumber) return
      
      if (this.previousNumber && this.operation && this.currentNumber) {
        this.calculate()
      }
      
      this.operation = op
      this.previousNumber = this.currentNumber
      this.currentNumber = ''
      this.expression = `${this.previousNumber} ${this.operation}`
      this.display = this.expression
    },

    calculate() {
      if (!this.previousNumber || !this.currentNumber || !this.operation) return

      const prev = parseFloat(this.previousNumber)
      const current = parseFloat(this.currentNumber)
      let result

      switch(this.operation) {
        case '+': result = prev + current; break
        case '-': result = prev - current; break
        case '×': result = prev * current; break
        case '÷': 
          if (current === 0) {
            this.display = 'Hata'
            return
          }
          result = prev / current
          break
      }

      this.currentNumber = result.toString()
      this.expression = this.currentNumber
      this.display = this.expression
      this.previousNumber = ''
      this.operation = null
    },

    deleteLastDigit() {
      if (this.currentNumber) {
        this.currentNumber = this.currentNumber.slice(0, -1)
        // Eğer tüm rakamlar silindiyse
        if (!this.currentNumber) {
          this.currentNumber = '0'
        }
        this.display = this.currentNumber
      }
    },

    clear() {
      this.display = ''
      this.currentNumber = ''
      this.previousNumber = ''
      this.operation = null
      this.expression = ''
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
        'operator': ['+', '-', '×', '÷','<'].includes(button),
        'function': ['±', '%'].includes(button),
        'clear': button === 'AC',
        'mode': button === '⏺',
        'equals': button === '⏺️',
        'number': !isNaN(button) || button === '.'
      }
    }
  },
  computed: {
    liveResult() {
      if (!this.currentNumber || !this.operation || !this.previousNumber) {
        return '';
      }

      const prev = parseFloat(this.previousNumber);
      const current = parseFloat(this.currentNumber);

      switch(this.operation) {
        case '+': return `${prev + current}`;
        case '-': return `${prev - current}`;
        case '×': return `${prev * current}`;
        case '÷': return current !== 0 ? `${prev / current}` : 'Hata';
        default: return '';
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
}

.calculator {
  padding: ;
  font-size: 24px;
}

.display {
  color: #CCCDD7;
  padding: 20px;
  text-align: right;
  font-size: 2em;
  margin-bottom: 20px;
  border-radius: 5px;
  min-height: 150px;
  word-break: break-all;
}

.live-result {
  font-family: 'SF Pro Display', sans-serif;
  font-size: 24px;
  color: white;
}

.keypad {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: minmax(60px, auto);
  gap: 12px;
}
.result {
  font-family: 'SF Pro Display', sans-serif;
  font-size: 64px;
  font-weight: 500;
  color: white;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  background: linear-gradient(180deg, #FFFFFF 0%, rgba(255, 255, 255, 0.8) 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  margin: 0;
  line-height: 1.2;
  letter-spacing: -0.5px;
  word-break: break-all;
  max-width: 100%;
}
button {
  padding: 20px;
  font-size: 1.2em;
  border: none;
 color: white;
 opacity: 100%;
  cursor: pointer;
  transition: 0.2s;
  user-select: none;
  user-select: none;
  -webkit-tap-highlight-color: transparent; /* Mobil tıklama efektini kaldır */
  outline: none; /* Tıklama çerçevesini kaldır */
}
/* button:focus {
  outline: none; 
}

button:active {
  background: inherit; 
} */
button.number {
  background: #;
  color: white;
  border-radius: 100%;
}

button.operator {
  background: #fff;
  color: white;
  border-radius: 100%;
  background-color: rgb(255, 255, 255, 0.05); /* 30% opacity ile beyaz */
}
button.clear {
  background-image: linear-gradient(to bottom, #ED0E98, #FE5A2D);
  background-clip: text;
  color: transparent;
}
button.function {
  color: white;
  border-radius: 100%;
  background-color: rgb(255, 255, 255, 0.05); /* 30% opacity ile beyaz */
}

button.equals {
  background-image: linear-gradient(to bottom, #ED0E98, #FE5A2D);
  color: white;
  grid-row: span 2; 
  grid-column: 4;
  min-height: 132px; /* İki buton + bir gap yüksekliği */
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50px;
}
button.mode {
 opacity: 100%;
  border-radius: 100%;
  border-width: 2px;
  border: rgb(255, 255, 255, 0.05); /* 30% opacity ile beyaz */
}
button.equals img {
  width: 24px;
  height: 24px;
  margin: auto;
}

button:hover {
  opacity: 0.8;
}

button:active {
  transform: scale(0.95);
}

.keypad button {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 60px;
  height: 60px;
}

.keypad button img {
  width: 24px;
  height: 24px;
}
</style>
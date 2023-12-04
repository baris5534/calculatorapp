<template>
    <div class="h-screen flex bg-slate-900">
        <div class="grid grid-cols-4 w-72 gap-2 border-2 border-solid border-white text-xl mx-auto font-bold bg-cyan-900 p-3 my-auto">
            <span class="col-span-3 text-white flex ">Calculator</span>
            <div class="col-span-4 bg-slate-400 p-5 text-end break-words">{{ calculatorValue || 0 }}</div>
            <button v-for="n in calculatorElements" :key="n">
                <div class="text-white text-center bg-orange-600 p-5" 
                :class="{'bg-vue-green':['C','*','/','-','+','%','='].includes(n)}" @click="action(n)">
                    {{ n }}
                </div>
            </button>
            <!-- <button class="bg-orange-400 p-5 text-red-800">C</button>
            <button class="bg-orange-400 p-5">%</button>
            <button class="bg-orange-400 p-5">()</button>
            <button class="bg-orange-400 p-5">/</button>
            <button class="bg-slate-400 p-5">7</button>
            <button class="bg-slate-400 p-5">8</button>
            <button class="bg-slate-400 p-5">9</button>
            <button class="bg-orange-400 p-5">+</button>
            <button class="bg-slate-400 p-5">4</button>
            <button class="bg-slate-400 p-5">5</button>
            <button class="bg-slate-400 p-5">6</button>
            <button class="bg-orange-400 p-5">-</button>
            <button class="bg-slate-400 p-5">1</button>
            <button class="bg-slate-400 p-5">2</button>
            <button class="bg-slate-400 p-5">3</button>
            <button class="bg-orange-400 p-5">*</button>
            <button class="bg-slate-400 p-5 col-span-2">0</button>
            <button class="bg-slate-400 p-5">.</button>
            <button class="bg-green-400 p-5">=</button> -->

        </div>
    </div>
</template>

<script>

    export default {
        name:'CalculatorApp',
        props:{
            msg:String
        },
        data(){
        return{
            calculatorValue:'',
            calculatorElements:['C','%','/','-',7,8,9,'+',4,5,6,'*',1,2,3,'=',0,'.'],
            operator:null,
            previousCalculatorValue:'',
        }
    },
    methods:{
        action(n){
            // append value
            if(!isNaN(n) || n === '.'){
                this.calculatorValue += n + '';
            }
            // sıfırlama    
            if(n === 'C'){
                this.calculatorValue = '';
            }
            // yüzdelik       
            if(n === '%'){
                this.calculatorValue = this.calculatorValue / 100 + '';
            }
            // 
            if(['/','*','-','+'].includes(n)){
                this.operator = n;
                this.previousCalculatorValue = this.calculatorValue;
                this.calculatorValue = this.previousCalculatorValue + n + '' ;
            }
            // 
            if(n === '='){
                this.calculatorValue = eval(
                    this.previousCalculatorValue + this.operator + this.calculatorValue
                );
                this.previousCalculatorValue = '';
                this.operator = null;
            }
        }
    }
    }
    
</script>

<style scoped>
button ,div{
    border-radius: 5px;
}
button:hover{
    color:white;
}
.bg-vue-green{
    background:#3fb984;
}
div:nth-child(19){
    grid-column: span 3 / span 3;
}
</style>
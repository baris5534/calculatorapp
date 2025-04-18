<template>
  <div class="home h-full" >
    <CalculatorApp/>
  </div>
</template>

<script>
// @ is an alias to /src
import CalculatorApp from '@/components/CalculatorApp.vue'

export default {
  name: 'HomeView',
  components: {
    CalculatorApp
  }
}
</script>

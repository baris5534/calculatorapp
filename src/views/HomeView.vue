<template>
  <div class="home">
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

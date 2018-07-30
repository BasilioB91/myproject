<template>
  <div id="app">
  <h1> {{ title }} </h1>
    <Navbar />
    <Currencies />
  </div>
</template>

<script>
import Navbar from './Navbar';
import Currencies from './Currencies';
export default {
  name: 'app',
  components:{
    Navbar,
    Currencies
  },
  data () {
    return {
      title: 'Currency Rate Detector!'
    }
  }
}
</script>

<style>
h1{
  color: rgba(146, 0, 0, 0.664);
  font-weight: normal;
  text-align: center;
}
</style>

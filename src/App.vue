<template>
  <div id="app">
    <Header />
    <div class="container mx-auto">
      <router-view/>
    </div>
  </div>
</template>

<script>
import Header from '@/components/Header'

export default {
  components: {
    Header
  } 
}

</script>

<style lang="sass">

*
  margin: 0
  padding: 0
  box-sizing: border-box

#app 
  background-color: #f8fbfb

</style>

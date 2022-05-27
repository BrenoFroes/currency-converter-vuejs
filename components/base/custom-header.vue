<template>
  <div>
    <img :src="svgHash" class="mx-auto mb-20">
    <h1 class="text-3xl font-semibold text-blue-300 text-center">Criptocurrency Quote</h1>
  </div>
</template>
<script>
export default {
  name: 'CustomHeader',
  data() {
    return {
      svgHash: require("../../assets/svg/hashdex.svg")
    }
  }
}
</script>
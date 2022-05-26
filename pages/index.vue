<template>
  <div class="w-1/2 mx-auto my-20">
    <h1 class="text-3xl font-semibold text-blue-300 text-center">Criptocurrency Quote</h1>
    <form class="mt-20 mb-5" id="add-cripto">
      <label class="mr-5" for="cripto-name">Name:</label>
      <input type="text" class="border-solid border-2 border-sky-500 hover:border-double mr-5" placeholder="ex: Bitcoin"
        id="cripto-name">
      <label class="mr-5" for="cripto-name">Current value:</label>
      <input type="text" class="border-solid border-2 border-sky-500 hover:border-double mr-5" placeholder="ex: Bitcoin"
        id="cripto-name">
      <button type="submit" class="bg-blue-300 hover:bg-blue-500 text-white font-bold py-2 px-4 rounded mr-5">Add</button>
    </form>
    <hr>
    <button v-on:click="loadListCripts()">Load criptos</button>
    {{ criptoList }}
    <table class="w-full text-left">
      <thead>
        <tr>
          <th>Name</th>
          <th>Value</th>
          <th>USD ($)</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Bitcoin</td>
          <td>1</td>
          <td>29.370</td>
        </tr>
        <tr>
          <td>Etherum</td>
          <td>1</td>
          <td>1.855</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data() {
    return {
      criptoList: []
    }
  },
  methods: {
    loadListCripts() {
      this.$axios
        .$get('https://rest.coinapi.io/v1/exchanges', {
          headers: {
            'X-CoinAPI-Key': 'C0012C6B-347D-4FE3-9F48-11A3E53FEDAE'
          }
        })
        .then(function (response) {
          console.log(response.data);
          return response;
        })
        .catch(function (error) {
          return error;
        });
    },
  },
}
</script>

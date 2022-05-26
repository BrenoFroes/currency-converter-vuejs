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
    <table class="w-full text-left border-solid border-2 border-sky-500 rounded mt-5">
      <thead>
        <tr>
          <th>Name</th>
          <th>ID</th>
          <th>Value</th>
          <th>USD ($)</th>
        </tr>
      </thead>
      <tbody v-for="cripto in criptoList">
        <tr>
          <td> {{ cripto.name }} </td>
          <td> {{ cripto.asset_id }} </td>
          <td>1</td>
          <td> {{ cripto.price_usd }} </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
  export default {
    name: 'IndexPage',
    data () {
      return {
        criptoList: [],
        namesList: ["BTC","ETH","USDT","BNB","USDC","XRP","LUNA","ADA","SOL","BUSD"],
        transformList: ""
      }
    },
    methods: {
      loadListCripts() {
        this.$axios
          .$get(`https://rest.coinapi.io/v1/assets?filter_asset_id=${this.transformList}`, {
            headers: {
              'X-CoinAPI-Key': 'C0012C6B-347D-4FE3-9F48-11A3E53FEDAE'
            }
          })
          .then((response) => {
            console.log(response);
            this.criptoList = response;
          })
          .catch(function (error) {
            console.log(error);
          });
      },
      adaptedList(){
        this.transformList = this.namesList.toString();
        this.transformList = this.transformList.replace('"','');
        this.transformList = this.transformList.replace('[','');
        this.transformList = this.transformList.replace(']','');
        console.log(this.adaptedList);
      }
    }, 
    mounted: function() {
      this.adaptedList();
      this.loadListCripts();  
    }
  }
</script>

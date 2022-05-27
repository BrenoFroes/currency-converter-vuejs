<template>
  <div class="w-4/5 lg:w-1/2 mx-auto my-20">
    <h1 class="text-3xl font-semibold text-blue-300 text-center">Criptocurrency Quote</h1>
    <div class="flex items-center justify-start mt-20 mb-5">
      <form id="usd-value">
        <label class="mr-5" for="cripto-name">USD value:</label>
        <input type="text" class="border-solid border-2 border-sky-500 hover:border-double mr-5" placeholder="ex: 20.00"
          id="cripto-name" v-model="usdValue" @keyup.prevent="convertValues()">
      </form>
      <form id="add-cripto" @submit.prevent="addCripto()">
        <label class="mr-5" for="cripto-name">Cripto name:</label>
        <input type="text" class="border-solid border-2 border-sky-500 hover:border-double mr-5"
          placeholder="ex: Bitcoin" id="cripto-name" v-model="newCripto">
        <button type="submit"
          class="bg-blue-500 hover:bg-blue-300 text-white font-bold py-2 px-4 rounded mr-5">Add</button>
      </form>
    </div>
    <hr>
    <div v-if="!loading && erro" class="error mx-auto mt-10 flex items-center flex-col">
      <img :src="svgError">
      <h2 class="text-red-500 text-2xl font-bold text-center">ERROR</h2>
    </div>
    <img v-if="loading" class="mx-auto mt-10" :src="svgLoading">
    <table v-if="!erro && !loading"
      class="w-full text-left border-solid border-2 border-sky-500 rounded mt-5 table-auto max-w-full">
      <thead>
        <tr>
          <th>Name</th>
          <th>ID</th>
          <th>coin/$</th>
          <th v-if="usdValue">USD ($)</th>
          <th v-if="usdValue">Coins</th>
        </tr>
      </thead>
      <tbody v-for="(item, index) in criptoList">
        <tr>
          <td> {{ item.name }} </td>
          <td> {{ item.asset_id }} </td>
          <td> {{ quoteList[index] }} </td>
          <td v-if="usdValue"> {{ usdValue }} </td>
          <td v-if="usdValue"> {{ newQuoteList[index] }} </td>
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
      svgLoading: require('../assets/svg/bars.svg'),
      svgError: require('../assets/svg/error.svg'),
      namesList: ["BTC", "ETH", "USDT", "BNB", "USDC", "XRP", "LUNA", "ADA", "SOL", "BUSD"],
      transformList: "",
      newCripto: "",
      newQuoteList: [],
      criptoList: [],
      quoteList: [],
      usdValue: null,
      erro: false,
      loading: false
    }
  },
  comuputed: {
  },
  methods: {
    async loadListCripts() {
      this.loading = true;
      let vm = this;
      // Convert list to string
      this.transformList = this.namesList.toString();
      this.transformList = this.transformList.replace('"', '');
      this.transformList = this.transformList.replace('[', '');
      this.transformList = this.transformList.replace(']', '');

      // Call axios get assets
      await this.$axios
        .$get(`https://rest.coinapi.io/v1/assets?filter_asset_id=${this.transformList}`, {
          headers: {
            'X-CoinAPI-Key': '8AD4B576-5692-4472-BC41-4D2A9CD09FE9'
          }
        })
        .then((response) => {
          if (response[0]) {
            this.erro = false;
            this.criptoList = response;
            console.log(response);
            for (let i = 0;i < this.criptoList.length;i++) {
              let resultOne = 1 / this.criptoList[i].price_usd;
              this.quoteList.push(resultOne);
            }
          } else {
            this.erro = true;
          }
          this.loading = false;
        })
        .catch(function (error) {
          vm.erro = true;
          vm.loading = false;
        });
    },
    async addCripto() {
      this.loading = true;
      let vm = this;
      // Call axios get assets
      await this.$axios
        .$get(`https://rest.coinapi.io/v1/assets/${this.newCripto}`, {
          headers: {
            'X-CoinAPI-Key': '8AD4B576-5692-4472-BC41-4D2A9CD09FE9'
          }
        })
        .then((response) => {
          if (response && response[0].asset_id != "USD") {
            this.erro = false;
            this.criptoList.push(response[0]);
            let sizeArray = this.criptoList.length - 1;
            this.namesList.push(this.criptoList[sizeArray].asset_id);
            let resultNew = 1 / this.criptoList[sizeArray].price_usd;
            let resultNewUSD = this.usdValue / this.criptoList[sizeArray].price_usd;
            this.newQuoteList.push(resultNewUSD);
            this.quoteList.push(resultNew);
          } else {
            this.erro = true;
          }
          this.loading = false;
        })
        .catch(function (error) {
          vm.erro = true;
          vm.loading = false;
        });
    },
    convertValues() {
      for (let i = 0;i < this.criptoList.length;i++) {
        let result = this.usdValue / this.criptoList[i].price_usd;
        this.newQuoteList[i] = result;
      }
      this.loadListCripts();
    }
  },
  computed: {
  },
  mounted: function () {
    var x = this.loadListCripts();
    // setInterval(x, 3000);
  }
}
</script>

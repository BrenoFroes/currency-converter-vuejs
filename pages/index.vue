<template>
  <div class="w-full md:w-3/4 mx-5 md:mx-auto my-20">
    <CustomHeader></CustomHeader>
    <div class="flex items-center justify-start items-start flex-col sm:flex-row mt-10 mb-5">
      <form id="usd-value" class="w-auto mt-5 sm:mt-0 flex flex-col sm:flex-row mr-auto sm:items-center">
        <label class="mr-5" for="cripto-name">USD value:</label>
        <input type="text" class="border-solid border-2 mr-5" placeholder="ex: 20.00" id="cripto-name"
          v-model="usdValue" @keyup.prevent="convertValues()">
      </form>
      <form id="add-cripto" @submit.prevent="addCripto()"
        class="w-auto mt-5 sm:mt-0 flex flex-col sm:flex-row mr-auto sm:items-center">
        <label class="sm:mr-5 mr-1" for="cripto-name">Cripto name:</label>
        <input type="text" class="border-solid border-2 sm:mr-5 mr-1" placeholder="ex: Bitcoin" id="cripto-name"
          v-model="newCripto">
        <button type="submit"
          class="bg-blue-500 hover:bg-blue-300 text-white font-bold sm:py-2 sm:px-4 py-1 px-2 rounded mt-5 sm:mt-2 justify-start mr-0 sm:mr-5">Add</button>
      </form>
    </div>
    <hr>
    <div v-if="!loading && erro" class="error mx-auto mt-10 flex items-center flex-col">
      <img :src="svgError">
      <h2 class="text-red-500 text-2xl font-bold text-center">ERROR</h2>
    </div>
    <img v-if="loading" class="mx-auto mt-10" :src="svgLoading">
    <table v-if="!erro && !loading"
      class="w-auto sm:w-full text-left border-solid border-2 border-sky-500 rounded mt-5 table-fixed max-w-full">
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
import CustomHeader from '../components/base/custom-header.vue'
export default {
  name: "IndexPage",
  components: { CustomHeader },
  data() {
    return {
      svgLoading: require("../assets/svg/bars.svg"),
      svgError: require("../assets/svg/error.svg"),
      coinKey: "DF0FBFA7-62B3-48EE-AC9E-E9AEDDF7592F",
      namesList: ["BTC", "ETH", "USDT", "BNB", "USDC", "XRP", "LUNA", "ADA", "SOL", "BUSD"],
      newCripto: "",
      newQuoteList: [],
      criptoList: [],
      quoteList: [],
      usdValue: null,
      erro: false,
      loading: false
    };
  },
  comuputed: {},
  methods: {
    async loadListCripts() {
      this.loading = true;
      let vm = this;
      let transformList = "";

      // Convert list to string
      transformList = this.namesList.toString();
      transformList = transformList.replace("\"", "");
      transformList = transformList.replace("[", "");
      transformList = transformList.replace("]", "");

      // Call axios get assets
      await this.$axios
        .$get(`https://rest.coinapi.io/v1/assets?filter_asset_id=${transformList}`, {
          headers: {
            "X-CoinAPI-Key": `${this.coinKey}`
          }
        })
        .then((response) => {
          if (response[0]) {
            this.erro = false;
            this.criptoList = response;
            for (let i = 0;i < this.criptoList.length;i++) {
              // Set calculated value in 1$ and push to array
              let resultOne = 1 / this.criptoList[i].price_usd;
              this.quoteList.push(resultOne);
            }
          }
          else {
            this.erro = true;
          }
          this.loading = false;
        })
        .catch(() => {
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
            "X-CoinAPI-Key": `${this.coinKey}`
          }
        })
        .then((response) => {
          if (response && response[0].asset_id != "USD") {
            this.erro = false;
            this.criptoList.push(response[0]);

            // Get last position array
            let sizeArray = this.criptoList.length - 1;
            this.namesList.push(this.criptoList[sizeArray].asset_id);

            // Set calculated value in 1$
            let resultNew = 1 / this.criptoList[sizeArray].price_usd;

            // Set calculated value in infomated USD
            let resultNewUSD = this.usdValue / this.criptoList[sizeArray].price_usd;

            // Push values in arrays
            this.newQuoteList.push(resultNewUSD);
            this.quoteList.push(resultNew);
          }
          else {
            this.erro = true;
          }
          this.loading = false;
        })
        .catch(() => {
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
  mounted: function () {
    this.loadListCripts();
    window.setInterval(() => {
      this.loadListCripts();
    }, 30000)
  }
}
</script>

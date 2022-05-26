<template>
  <div class="w-4/5 lg:w-1/2 mx-auto my-20">
    <h1 class="text-3xl font-semibold text-blue-300 text-center">Criptocurrency Quote</h1>
    <form class="mt-20 mb-5" id="add-cripto">
      <label class="mr-5" for="cripto-name">USD value:</label>
      <input type="text" class="border-solid border-2 border-sky-500 hover:border-double mr-5" placeholder="ex: 20.00"
        id="cripto-name" v-model="usdValue" @keyup.prevent="convertValues()">
      <label class="mr-5" for="cripto-name">Cripto name:</label>
      <input type="text" class="border-solid border-2 border-sky-500 hover:border-double mr-5" placeholder="ex: Bitcoin"
        id="cripto-name" v-model="newCripto">
      <button type="submit" class="bg-blue-300 hover:bg-blue-500 text-white font-bold py-2 px-4 rounded mr-5"
        @click.prevent="addCripto()">Add</button>
    </form>
    <hr>
    <table class="w-full text-left border-solid border-2 border-sky-500 rounded mt-5 table-auto max-w-full">
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
      namesList: ["BTC", "ETH", "USDT", "BNB", "USDC", "XRP", "LUNA", "ADA", "SOL", "BUSD"],
      transformList: "",
      newCripto: "",
      newQuoteList: [],
      criptoList: [],
      quoteList: [],
      usdValue: null
    }
  },
  comuputed: {
  },
  methods: {
    async loadListCripts() {
      // Convert list to string
      this.transformList = this.namesList.toString();
      this.transformList = this.transformList.replace('"', '');
      this.transformList = this.transformList.replace('[', '');
      this.transformList = this.transformList.replace(']', '');

      // Call axios get assets
      await this.$axios
        .$get(`https://rest.coinapi.io/v1/assets?filter_asset_id=${this.transformList}`, {
          headers: {
            'X-CoinAPI-Key': '40D216D5-A81D-4A9D-81D2-0E2D65AC72D8'
          }
        })
        .then((response) => {
          this.criptoList = response;
        })
        .catch(function (error) {
          console.log(error);
        });

      for (let i = 0;i < this.criptoList.length;i++) {
        let resultOne = 1 / this.criptoList[i].price_usd;
        this.quoteList.push(resultOne);
      }

    },
    addCripto() {
      this.namesList.push(this.newCripto);
      this.loadListCripts();
    },
  },
  computed: {
    convertValues() {
      for (let i = 0;i < this.criptoList.length;i++) {
        let result = this.usdValue / this.criptoList[i].price_usd;
        this.newQuoteList[i] = result;
        console.log(this.newQuoteList[i]);
      }
      console.log(this.newQuoteList);
      console.log("+++++++++");
    }
  },
  mounted: function () {
    this.loadListCripts();
  }
}
</script>

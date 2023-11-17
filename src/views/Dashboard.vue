<template>
  <div class="dashboard_section">
    <div class="header">
      <h2>Ethereum</h2>
      <div class="navbar">
        <select
          class="selectBox"
          name="leanguage"
          id="leanguage"
          @change="handleSelectChange"
        >
          <option value="en">English</option>
          <option value="ru">Russian</option>
          <option value="ja">Japanese</option>
        </select>
        <button class="logout_btn" @click="logout">logout</button>
      </div>
    </div>

    <div class="content">
      <h1>Description</h1>
      <p class="description" v-if="description?.[selectedOption] !== ''">
        {{ description?.[selectedOption] }}
      </p>
      <p class="no_content" v-else>no description</p>
      <div v-if="data" class="price_section">
        <h2>Current price</h2>
        <p>USD : {{ currentUsdPrice }}</p>
        <p>BTC : {{ currentBtcPrice }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      data: null,
      currentUsdPrice: null,
      currentBtcPrice: null,
      description: null,
      selectedOption: "en",
    };
  },
  methods: {
    handleSelectChange(e) {
      const { value } = e.target;
      this.selectedOption = value;
    },
    async fetchData() {
      try {
        // Make a GET request to the API
        const response = await axios.get(
          "https://api.coingecko.com/api/v3/coins/ethereum",
          {
            headers: {
              "Content-Type": "application/x-www-form-urlencoded",
            },
          }
        );
        console.log(response, "response here");
        // Set the data property with the response data
        this.data = response.data.market_data;
        this.currentUsdPrice = response.data.market_data.current_price.usd;
        this.currentBtcPrice = response.data.market_data.current_price.btc;
        this.description = response.data.description;
      } catch (error) {
        console.error("Error fetching data:", error);
      }
    },
    logout() {
      localStorage.removeItem("token");
      this.$router.push("/");
    },
  },
  beforeMount() {
    this.fetchData();
  },
  mounted() {
    // Set up an interval to increment the value every 40 seconds
    this.intervalId = setInterval(() => {
      // this.currentUsdPrice += 1;
      // this.currentBtcPrice += 1;
      this.fetchData();
    }, 40000); // 40 seconds in milliseconds
  },
  beforeDestroy() {
    // Clear the interval to avoid memory leaks when the component is destroyed
    clearInterval(this.intervalId);
  },
};
</script>

<style>
.dashboard_section {
  width: 100%;
  height: 100vh;
}

.header {
  width: 100%;
  height: 80px;
  background-color: antiquewhite;
  display: flex;
  padding: 0px 50px;
  justify-content: space-between;
  align-items: center;
}
.selectBox {
  padding: 5px;
  margin-right: 50px;
}
.navbar {
  width: 20%;
}
.header h2 {
  color: #3498db;
  font-weight: bold;
}
.content {
  padding: 20px 50px;
}
.price_section {
  display: flex;
  width: 100%;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
.price_section p {
  font-weight: bold;
}
.description {
  margin-top: 10px;
  width: 100%;
  min-height: 200px;
}

.no_content {
  margin-top: 10px;
  width: 100%;
  min-height: 200px;
  font-size: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  text-transform: capitalize;
  color: gray;
}
.logout_btn {
  padding: 10px 15px;
  background-color: #3498db;
  color: #fff;
  border: none;
  cursor: pointer;
  margin-top: 20px;
  text-transform: capitalize;
}
</style>

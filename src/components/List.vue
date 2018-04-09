<template>
  <div class="main">
    <div class="stats">
      <Average title="MPG" :avg="getAvgMpg()"></Average>
      <Average title="Price" :avg="getAvgPrice()"></Average>
    </div>
    <form>
      <label for="date">
        <input type="date" 
          id="date" 
          placeholder="date" 
          required
          :value="newItem.date && newItem.date"
          @input="newItem.date = $event.target.valueAsDate.toISOString().split('T')[0]"
        >
      </label>
      <label for="mileage">
        <input type="number" min=0 max=999999 id="mileage" placeholder="mileage" required v-model="newItem.mileage">
      </label>
      <label for="gallons">
        <input type="number" min=0 max=999 id="gallons" placeholder="gallons" required v-model="newItem.gallons">
      </label>
      <label for="price-per-gallon">
        <input type="number" min=0 max=9999 id="price-per-gallon" placeholder="price-per-gallon" required v-model.trim="newItem.price">
      </label>
      <button type="submit" @click="addItem" :disabled="buttonDisabled">Add</button>
    </form>
    <div class="list-container">
      <div class="list-container__header">
        <h4>Date</h4>
        <h4>Mileage</h4>
        <h4>Gallons</h4>
        <h4>Price</h4>
        <h4>MPG</h4>
      </div>
      <ul class="list" v-if="this.items && items.length">
        <ListItem
          v-for="item in items"
          :key="item.id"
          :item="item"
          @remove="removeItem($event)"
        >
        </ListItem>
      </ul>
      <p v-else>No entries found.</p>
    </div>
  </div>
</template>

<!-- store: https://www.jsonstore.io/ad585b7694616219f2cd59bcd6fdeda37073245af23d9362e3e33787f395e68b/ -->

<script>
import Average from "./Average";
import ListItem from "./ListItem";

export default {
  name: "List",
  components: {
    Average,
    ListItem
  },
  methods: {
    fetchRemoteData(url) {
      fetch(url)
        .then(response => {
          return response.json();
        })
        .then(data => {
          if (data.result) {
            data.result.forEach((item, index) => {
              item && (item.id = index);
            });
            this.items = data.result.filter(item => {
              return item !== null || undefined || "";
            });
          }
        })
        .catch(error => console.error("Error:", error));
    },
    postRemoteData(url, data) {
      fetch(url, {
        method: "POST", // or 'PUT'
        body: JSON.stringify(data), // data can be `string` or {object}!
        headers: new Headers({
          "Content-Type": "application/json"
        })
      })
        .then(res => res.json())
        .catch(error => console.error("Error:", error))
        .then(response => console.log("Success:", response));
    },
    fetchLocalData() {
      return localStorage.length
        ? JSON.parse(localStorage.getItem("items"))
        : null;
    },
    saveLocalData(data) {
      return data && data.length
        ? localStorage.setItem("items", JSON.stringify(data))
        : localStorage.clear();
    },
    resetNewItem() {
      this.newItem = {
        date: new Date().toISOString().split("T")[0],
        mileage: null,
        gallons: null,
        price: null
      };
    },
    getNewItemProperties() {
      this.getMileage();
      this.getGallons();
      this.getPrice();
      this.getMiles();
      this.getMpg();
    },
    addItem(e) {
      e.preventDefault();
      this.getNewItemProperties();
      this.newItem.id = this.items.length + 1;
      this.items && this.items.push(this.newItem);
      // this.saveLocalData(this.items);
      this.postRemoteData(this.url, this.items);
      this.resetNewItem();
    },
    removeItem(id) {
      this.items = this.items.filter(item => {
        return item.id !== id;
      });
      // this.saveLocalData(this.items);
      this.postRemoteData(this.url, this.items);
    },
    getMileage() {
      return (this.newItem.mileage =
        Math.round(this.newItem.mileage * 100) / 100);
    },
    getGallons() {
      return (this.newItem.gallons =
        Math.round(this.newItem.gallons * 100) / 100);
    },
    getPrice() {
      return (this.newItem.price = Math.round(this.newItem.price * 100) / 100);
    },
    getMiles() {
      const result =
        this.items && this.items.length > 0
          ? this.newItem.mileage - this.items[this.items.length - 1].mileage
          : 0;
      this.newItem.miles = result >= 1 ? Math.round(result * 100) / 100 : 0;
      return this.newItem.miles;
    },
    getMpg() {
      const result =
        this.newItem.miles && this.newItem.gallons
          ? (this.newItem.miles / this.newItem.gallons).toFixed(2)
          : 0;
      this.newItem.mpg = result >= 1 ? Math.round(result * 100) / 100 : 0;
      return this.newItem.mpg;
    },
    getAvgMpg() {
      let mpgArray = [];
      this.items &&
        this.items.length > 1 &&
        this.items.forEach(item => mpgArray.push(item.mpg));
      const result = (
        mpgArray.length && mpgArray.reduce((a, b) => a + b) / this.items.length
      ).toFixed(2);
      return result > 0 ? Math.round(result * 100) / 100 : 0;
    },
    getAvgPrice() {
      let priceArray = [];
      this.items &&
        this.items.length &&
        this.items.forEach(item => priceArray.push(item.price));
      const result = (
        priceArray.length &&
        priceArray.reduce((a, b) => a + b) / this.items.length
      ).toFixed(2);
      return result >= 0 ? "$" + Math.round(result * 100) / 100 : 0;
    }
  },
  data() {
    return {
      url:
        "https://www.jsonstore.io/b5f70be0cbf39e0e0d27cfe380b92a1d0ab6926ce83845f9934d47a21c54ac8c/",
      avg: 0,
      newItem: {
        date: new Date().toISOString().split("T")[0],
        mileage: null,
        gallons: null,
        price: null
      },
      items: []
    };
  },
  computed: {
    buttonDisabled: function() {
      const item = Object.values(this.newItem);
      return (
        item.includes(null) ||
        item.includes(undefined) ||
        item.includes("") ||
        item.includes(" ") ||
        item.includes("0")
      );
    }
  },
  mounted() {
    this.fetchRemoteData(this.url);
    this.newItem.id = this.items.length + 1;
  }
};
</script>

<style scoped>
.main {
  margin: 0 auto;
}
.stats {
  display: flex;
  flex-flow: row wrap;
  margin: 0.5rem;
}
form {
  margin: 0.5rem;
}
input {
  display: block;
  border-top: none;
  border-right: none;
  border-bottom: 1px solid #eee;
  border-left: none;
  box-sizing: border-box;
  margin: 0 auto 1rem auto;
  padding: 1rem 0;
  width: 100%;
  outline: none;
  transition: all 200ms ease-in-out;
}
input:focus {
  border-bottom: 1px solid #66bb6a;
}
button {
  background: #66bb6a;
  border: none;
  color: #fff;
  padding: 1rem;
  text-transform: uppercase;
  width: 100%;
  cursor: pointer;
  transition: all 200ms ease-in-out;
}
button:hover,
button:focus,
button:active {
  background: #338a3e;
}
button:disabled {
  cursor: not-allowed;
  background: #eee;
}
.list-container {
  margin: 0 auto;
}
.list-container__header {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
}
.list {
  list-style-type: none;
  margin: 0;
  padding: 0;
}
</style>

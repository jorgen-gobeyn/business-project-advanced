<template>
  <div class="hello">
    <div class="canvas" @click="modal = false"></div>
    <h1>{{ msg }}</h1>
    <div class="input">
      <select>
        <option value="" disabled selected>Selecteer een klant.</option>
        <option v-for="party in parties" :key="party.id">{{ party }}</option>
    </select>
  </div>    
    <div class="input">
      <input type="text" autocomplete="off" v-model="product" @input="filterProducts" @focus="modal = true">
      <div v-if="filteredProducts && modal">
        <ul>
          <li v-for="filteredProduct in filteredProducts" :key="filteredProduct.id" @click="setProduct(filteredProduct)">{{ filteredProduct.slice(7) }}</li>
        </ul>
      </div>
      <p>{{ coords }}</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";
let apiKey = "739d1ee1-2fb4-418f-a906-d9d788eed921";
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      parties: [],
      product: '',
      products: [],
      filteredProducts: [],
      modal: false,
      coords: {
        lat: 0,
        lng: 0,
      },
    };
  },
  async created() {
    this.getLocation();
    await this.getData();
  },

  methods: {
    getLocation() {
      const isSupported = 'navigator' in window && 'geolocation' in navigator;
      if (isSupported) {
        navigator.geolocation.getCurrentPosition(
          position => {
            this.coords.lat = position.coords.latitude;
            this.coords.lng = position.coords.longitude;
          }
        );
      } else {
        console.log("Geolocation is not supported by this browser.");
      }
    },
    async getData() {
      const config = {
        headers: {
          'Accept': 'application/json', 'apikey': apiKey,
        },
      };
      //Klanten ophalen
      let res = await axios.get(
        "https://api.sandbox.billit.be/v1/parties",
        config
      );
      for(let i = 0; i < res.data.Items.length; i++) {
        //Naam en adres van klant uit API
        let client = res.data.Items[i].ContactFirstName + " " + res.data.Items[i].ContactLastName + ", " + res.data.Items[i].Street + " " + 
                      res.data.Items[i].StreetNumber + ", " + res.data.Items[i].Zipcode + " " + res.data.Items[i].City;
        console.log(client);
        this.parties.push(client);
      }
      //Eerste 120 Producten ophalen
      res = await axios.get(
        "https://api.sandbox.billit.be/v1/products",
        config
      );
      for(let i = 0; i < res.data.Items.length; i++) {
        let product = res.data.Items[i].ProductID + " " + res.data.Items[i].Reference + " " + res.data.Items[i].Description;
        console.log(product);
        this.products.push(product);
      }
      //Volgende 120 Producten ophalen
      res = await axios.get(
        "https://api.sandbox.billit.be/v1/products?$skip=120",
        config
      );
      for(let i = 0; i < res.data.Items.length; i++) {
        let product = res.data.Items[i].ProductID + " " + res.data.Items[i].Reference + " " + res.data.Items[i].Description;
        console.log(product);
        this.products.push(product);
      }
      //Resterende Producten ophalen
      res = await axios.get(
        "https://api.sandbox.billit.be/v1/products?$skip=240",
        config
      );
      for(let i = 0; i < res.data.Items.length; i++) {
        let product = res.data.Items[i].ProductID + " " + res.data.Items[i].Reference + " " + res.data.Items[i].Description;
        console.log(product);
        this.products.push(product);
      }
    },

    filterProducts() {
      this.filteredProducts = this.products.filter(product => {
        return product.toLowerCase().includes(this.product.toLowerCase())
           
      })
      console.log(this.filteredProducts)  
    },

    setProduct(product) {
      this.product = product.slice(7);
      this.modal = false;
      console.log(product);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
div {
  color: #42b983;
}
.input{
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin-top: 5vh;
  z-index: 10;
}
.canvas {
  width: 100%;
  height: 100%;
  position: absolute;
  z-index: -1;
}
li {
  list-style: none;
  text-align: left;
  padding-left: 10vh;
  cursor: pointer;
}
</style>

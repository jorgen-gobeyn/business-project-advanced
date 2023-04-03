<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <button @click="showData">Click Me</button>
    <p></p>
    <div v-if="info">{{ info }}</div>
    <option v-if="parties">{{ parties }}</option>
  </div>
</template>

<script>
import axios from "axios";
let key = "739d1ee1-2fb4-418f-a906-d9d788eed921";
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      info: '',
      parties: [],
    };
  },
  // created() {
  //   this.$getLocation({})
  //     .then((location) => {
  //       console.log(location);//Location van de gebruiker
  //     })
  // },

  methods: {
    async showData() {
      this.parties = [];
      let config = {
        headers: {
          'Accept': 'application/json', 'apiKey': key,
        },
      };
      let res = await axios.get(
        "https://api.sandbox.billit.be/v1/parties",
        config
      );
      for(let i = 0; i < res.data.Items.length; i++) {
        console.log(res.data.Items[i].Name);
        this.parties.push(res.data.Items[i].Name + " " + res.data.Items[i].Street);//Naam van klant uit de API
      }
      res = await axios.get(
        "https://api.sandbox.billit.be/v1/account/accountInformation",
        config
      );
      this.info = res.data.Email;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>

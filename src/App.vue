<template>
  <v-app dark>
    <v-toolbar
      app
      clipped-left
    >
      <v-toolbar-title>Prize Bond Check</v-toolbar-title>
      <v-spacer />
    </v-toolbar>
    <v-content>
      <v-container align-center>
        <v-textarea
          v-model="input"
          label="Insert Column"
          @change="search"
        />
        <v-btn block>
          Check
        </v-btn>
        <br />
        <div v-html="result" id="result" />
      </v-container>
      <Snackbar ref="snackbar" />
    </v-content>
  </v-app>
</template>

<script>
import Snackbar from "./components/Snackbar";
import marked from "marked";
import axios from "axios";

export default {
  name: 'App',
  components: {
    Snackbar
  },
  data () {
    return {
      input: '',
      result: ''
    }
  },
  methods: {
    showSnackbar: function(type, message) {
      this.$refs.snackbar.show(type, message);
    },
    search: function(input) {
      const lines = input.split('\n');

      if (lines.length < 2)
        return
      
      const bondType = lines.shift();
      let searchQuery = '';

      lines.forEach(number => {
        if (parseInt(number))
          searchQuery += `${number},${number}|`
      });

      const targetUrl = `https://hamariweb.com/finance/GetPriceBondResult.aspx?d=&b=${bondType}&s=${searchQuery}`;

      axios.get(targetUrl).then(res => {
        this.result = res.data;
      }).catch(err => {
        this.showSnackbar("error", String(err));
      })
    }
  }
}
</script>

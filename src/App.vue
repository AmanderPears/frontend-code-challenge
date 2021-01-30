<template>
  <div>
    <label for="maxCP" class="max-cp">
      <input type="checkbox" id="maxCP" v-model="sortByMaxCP" />
      <small> Maximum Combat Points </small>
    </label>
    <input
      type="text"
      class="input"
      placeholder="Pokemon or type"
      @blur="reset"
      v-model="query"
      v-on:click="fetchOnUserInput()"
    />
    <div class="loader" v-if="loadingJson"></div>
    <ul class="suggestions">
      <li v-if="filtered.length == 0">
        <img
          src="https://cyndiquil721.files.wordpress.com/2014/02/missingno.png"
          alt="No results"
        />
        <div class="info">
          <h1 class="no-results">No results</h1>
        </div>
      </li>

      <!-- filtered list-->
      <li v-for="(item, key) in filtered" :key="key">
        <img v-bind:src="item.img" v-bind:alt="item.Name" />
        <div class="info">
          <h1><span v-html="highlight(item.Name)"></span></h1>
          <span
            v-for="(type, typeKey) in item.Types"
            :key="typeKey"
            v-bind:class="'type ' + type.toLowerCase()"
            >{{ type }}</span
          >
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
// eslint-disable-next-line no-unused-vars
const URL_PATH =
  "https://gist.githubusercontent.com/bar0191/fae6084225b608f25e98b733864a102b/raw/dea83ea9cf4a8a6022bfc89a8ae8df5ab05b6dcc/pokemon.json";

//var Vue = require("vue");

// new Vue({
//   data() {
//     return {
//       jsonData: null,
//     };
//   },
//   methods: {
//     fetchOnUserInput: function (url) {
//       fetch(url)
//         .then((res) => res.json())
//         .then((data) => {
//           this.jsonData = data;
//           console.log(data);
//         });
//     },
//   },
// });

export default {
  name: "App",
  data() {
    return {
      jsonData: null,
      query: "",
      loadingJson: false,
      sortByMaxCP: false
    };
  },
  computed: {
    filtered() {
      if (this.query != undefined && this.query.length > 0) {
        // filter by name
        let resultByName = this.jsonData.filter((item) => {
          return item["Name"].toLowerCase().includes(this.query.toLowerCase());
        });

        //filter by type
        let resultByType = this.jsonData.filter((item) => {
          return item.Types.some((t) =>
            t.toLowerCase().includes(this.query.toLowerCase())
          );
        });

        //append filter by type after name
        let result = resultByName.concat(resultByType);

        //sort by maxcp if checked
        if (this.sortByMaxCP) {
          result.sort((a, b) => b.MaxCP - a.MaxCP);
        }

        //show only 4 results
        result = result.slice(0, 4);

        return result;

        // return this.jsonData
        //   .filter((item) => {
        //     return (
        //       item["Name"].toLowerCase().includes(this.query.toLowerCase()) ||
        //       item.Types.some((t) =>
        //         t.toLowerCase().includes(this.query.toLowerCase())
        //       )
        //     );
        //   })
        //   .slice(0, 4);
      } else {
        return [];
      }
    },
    isEmpty() {
      if (typeof this.filtered === "undefined") {
        return false;
      } else {
        return this.filtered.length < 1;
      }
    },
  },
  methods: {
    fetchOnUserInput: function () {
      this.loadingJson = true;
      fetch(URL_PATH)
        .then((res) => res.json())
        .then((data) => {
          this.jsonData = data;
          this.loadingJson = false;
        });
    },

    reset() {
      this.query = "";
    },

    //function to create highlight text
    highlight(word) {
      return word.replace(
        new RegExp(this.query, "i"),
        "<span class='hl'>$&</span>"
      );
    },
  },
};
</script>

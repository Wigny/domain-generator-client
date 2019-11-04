<template>
  <div>
    <div id="main">
      <div class="container">
        <div class="row">
          <div class="col-md">
            <ItemList
              title="Prefixos"
              v-bind:items="prefixes"
              v-on:addItem="addPrefix"
              v-on:deleteItem="deletePrefix"
            ></ItemList>
          </div>
          <div class="col-md">
            <ItemList
              title="Sufixos"
              v-bind:items="sufixes"
              v-on:addItem="addSufix"
              v-on:deleteItem="deleteSufix"
            ></ItemList>
          </div>
        </div>
        <br />
        <h5>
          Domínios
          <span class="badge badge-info">{{domains.length}}</span>
        </h5>
        <div class="card">
          <div class="card-body">
            <ul class="list-group">
              <li class="list-group-item" v-for="domain in domains" v-bind:key="domain">{{ domain }}</li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios/dist/axios";
import ItemList from "./ItemList";

export default {
  name: "app",
  components: {
    ItemList
  },
  data() {
    return {
      prefixes: [],
      sufixes: [],
      domains: []
    };
  },
  methods: {
    addPrefix(prefix) {
      this.prefixes.push(prefix);
      this.generate();
    },
    addSufix(sufix) {
      this.sufixes.push(sufix);
      this.generate();
    },
    deletePrefix(prefix) {
      this.prefixes.splice(this.prefixes.indexOf(prefix), 1);
      this.generate();
    },
    deleteSufix(sufix) {
      this.sufixes.splice(this.sufixes.indexOf(sufix), 1);
      this.generate();
    },
    generate() {
      this.domains = [];
      for (const prefix of this.prefixes) {
        for (const sufix of this.sufixes) {
          this.domains.push(prefix + sufix);
        }
      }
    }
  },
  created() {
    axios({
      url: "http://localhost:4000",
      method: "post",
      data: {
        query: `{
          prefixes: items(type: "prefix") {
            id
            type
            description
          }
          sufixes: items(type: "sufix") {
            description
          }
        }`
      }
    }).then(({ data: { data: { prefixes, sufixes } } }) => {
      this.prefixes = prefixes.map(p => p.description);
      this.sufixes = sufixes.map(s => s.description);
    });
  }
};
</script>

<style>
</style>

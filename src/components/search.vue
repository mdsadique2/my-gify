<template>
  <div>
      <div class="width50 searchForm displayFlex">
          <input class="searchInput" placeholder="Search..." @input="searchForText($event)" v-model="searchText"/>
          <i class="searchIcon fas fa-search"></i>
      </div>
  </div>
</template>

<script>

import commons from '../utils/common.js'
export default {
  name: 'search',
  props: {
    inputValueText: {
      type: String,
      default: ''
    },
    searchFunction: {
      type: Function,
      default: () => {
      }
    },
    categoryOptions: {
      type: Array,
      default: () => {
        return []
      }
    }
  },
  data () {
    return {
      searchText: '',
      selectedCategory: {}
    }
  },
  methods: {
    searchForText: commons.debounce(function () {
      this.$emit('searchFunction', this.searchText);
    }, 500)
  },
  watch: {
    inputValueText (newVal, oldVal) {
      if (newVal !== oldVal) {
        this.searchText = newVal;
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
input {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
}
</style>

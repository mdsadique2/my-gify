<template>
  <div>
    <!-- top stcky portion with searchbar and category tags -->
    <div class="stickyContent">
      <search @searchFunction="searchThisInput" :inputValueText='currentSearchQuery' :categoryOptions="categories"></search>
      <div class="categoryPanel groups">
        <div class="category" v-if="showCategory">
          <div class="title">Available Categories:</div>
          <div>
            <div class="displayInlineBlock" v-for="(category, index) in categories" >
              <span class="tags left clickBg" @click="getGifForCategory(category)">{{category.name}}</span>
              <i class="tags right clickBg fas fa-caret-down" @click="selectCategory(index)"></i>
            </div>
          </div>
        </div>
        <div class="subCategory" v-if="!showCategory">
          <div @click="showCategoryPanel()" class="cursorPointer title hover displayInlineBlock">
            <i class="navigateIcons navigateIcon--left  fas fa-arrow-circle-left"></i>
            Subategories for <strong>'{{selectedCategory.name}}'</strong>:
          </div>
          <div>
            <button class="tags clickBg" v-for="(subCategory, index) in selectedCategory.subcategories" @click="selectSubCategory(index)">{{subCategory.name}}</button>
          </div>
        </div>
      </div>
      <!--  to display info about search -->
      <div class="resultHeader textAlignLeft">
        {{currentMessage}}
      </div>
    </div>

    <!--  Search result container that create grids of gif with search result -->
    <searchResults
      :searchKey="searchKeyText"
      :loader="fetchingGifs"
      :responseObj="responseData"
      :concatResults="concateIt"
      :searchType="currentSearchType"
      @searchAsTextFunction="searchSubcategoryAsText"
      :style="{'margin-top': stickyHeight}">
    </searchResults>

    <!-- clicking the button scrolls to the top -->
    <div class="scrollTopButton" @click="scrollToTop()">
      <i class="fas fa-arrow-circle-up"></i>
    </div>
  </div>
</template>

<script>
import search from '../components/search.vue'
import searchResults from '../components/searchResults.vue'
import commons from '../utils/common.js'

import Gify from '../sdk/gify'

export default {
  name: 'SearchPage',
  components: {
    search,
    searchResults
  },
  mounted () {
    (document.body).addEventListener('scroll', this.updateScroll);
  },

  created () {
    this.getCategories();
    this.getTrendingGifs();
    setTimeout(this.calculateStickyHeight, 1000);
  },
  computed: {
    currentMessage () {
      var obj = {
        subCategory: 'Showing result for Subcategory :' + this.searchKeyText,
        category: 'Showing result for Category : ' + this.searchKeyText,
        trending: 'Trending GIFs !',
        query: 'Showing result for text : ' + this.searchKeyText
      }
      return obj[this.currentSearchType]
    }
  },
  data () {
    return {
      responseData: {},
      page: 0,
      fetchingGifs: false,
      scrollPosition: null,
      categories: [],
      selectedCategory: {},
      showCategory: true,
      concateIt: false,
      categoryPanel: null,
      categoryPanelScrollHide: false,
      stickyHeight: '100px',
      currentSubCategory: '',
      currentSearchedText: '',
      currentSearchType: '',
      currentSearchQuery: '',
      currentCategory: '',
      searchKeyText: ''
    }
  },

  methods: {
    scrollToTop () {
      document.body.scrollTo(0, 0);
    },

    calculateStickyHeight () {
      var elm = (document.getElementsByClassName('stickyContent'))[0];
      var height = elm.clientHeight;
      this.stickyHeight = (height - 1) + 'px';
    },

    // selection of a category
    selectCategory (index) {
      this.selectedCategory = this.categories[index];
      this.showCategory = false;
    },

    // fired when search input is filled
    searchThisInput (inputVal) {
      var queryText = inputVal;
      if (typeof inputVal !== 'string') {
        queryText = inputVal.name
        this.currentSearchQuery = queryText;
      }
      this.getResult(queryText)
    },

    // switches between category and subcategory
    showCategoryPanel () {
      this.showCategory = true;
    },

    resetCategoryPanelHeight (type, value) {
      if (!this.categoryPanel) {
        this.categoryPanel = (document.getElementsByClassName('categoryPanel'))[0];
      }
      if (type === 'show') {
        this.categoryPanel.classList.remove('hide');
      } else {
        this.categoryPanel.classList.add('hide');
      }
    },

    // events with body scroll
    updateScroll (event) {
      this.scrollPosition = window.scrollTop;
      var element = event.target;
      if (element.scrollTop > 60 && this.categoryPanelScrollHide === false) {
        this.categoryPanelScrollHide = true;
        this.resetCategoryPanelHeight('hide', element.scrollTop);
      } else if (element.scrollTop < 60 && this.categoryPanelScrollHide === true){
        this.categoryPanelScrollHide = false;
        this.resetCategoryPanelHeight('show', element.scrollTop);
      }
      if (element.scrollHeight - element.scrollTop === element.clientHeight) {
        this.fetchApi();
      }
    },

    // scroll checks and call the required api
    fetchApi: commons.debounce( function() {
      if (this.fetchingGifs === true) {
        return
      }
      if (this.currentSearchType === 'subCategory') {
        this.selectSubCategory(this.currentSubCategory)
      } else if (this.currentSearchType === 'category') {
        this.getGifForCategory(this.currentCategory);
      } else if (this.currentSearchType === 'trending') {
        this.getTrendingGifs();
      } else {
        this.getResult(this.currentSearchedText);
      }
    },500),

    stopLoader () {
      var that = this;
      setTimeout(function(){
        that.fetchingGifs = false;
      }, 1000)
    },

    // not in use now
    searchSubcategoryAsText () {
      this.currentSearchQuery = this.currentSubCategory;
      this.getResult(this.currentSubCategory);
    },

    // gets list of categories
    getCategories () {
      Gify.categoriesForGifs({})
      .then((response) => {
        this.categories = response.data;
      })
      .catch((err) => {

      })
    },

    // to fetch gif of a category
    getGifForCategory (category) {
      if (this.currentSearchType !== 'category') {
        this.currentSearchType = 'category';
        this.concateIt = false;
        this.page = 0;
      }

      this.searchKeyText = category.name;
      if (this.currentCategory === category.name) {
        this.concateIt = true;
      } else {
        this.concateIt = false;
        this.currentCategory = category.name
      }
      this.fetchingGifs = true;
      Gify.subCategoriesForGifs(this.currentCategory, {
        offset: this.page++,
      })
      .then((response) => {
        this.responseData = response;
        this.stopLoader();
      })
      .catch((err) => {
        this.stopLoader();
      })

    },

    // to fetch gif of a subcategory
    selectSubCategory (index) {
      var subCategory = index;
      if (typeof index === 'number'){
        subCategory = this.selectedCategory.subcategories[index].name;
      }
      this.showCategory = false;
      if (this.currentSearchType !== 'subCategory') {
        this.currentSearchType = 'subCategory';
        this.concateIt = false;
        this.page = 0;
      }
      this.searchKeyText = subCategory;
      if (this.currentSubCategory === subCategory) {
        this.concateIt = true;
      } else {
        this.concateIt = false;
        this.currentSubCategory = subCategory
      }
      this.fetchingGifs = true;

      Gify.gifsByCategories(this.selectedCategory.name, subCategory, {
        offset: this.page++,
      })
      .then((response) => {
        this.responseData = response;
        this.stopLoader();
      })
      .catch((err) => {
        this.stopLoader();
      })
    },

    // to fetch gif of a search keyword
    getResult (queryText) {
      if (queryText.trim() === "") {
        this.getTrendingGifs();
        return;
      }
      if (this.currentSearchType !== 'query') {
        this.currentSearchType = 'query';
        this.concateIt = false;
        this.page = 0;
      }
      this.searchKeyText = queryText;
      if (this.currentSearchedText === queryText) {
        this.concateIt = true;
      } else {
        this.currentSearchedText = queryText
        this.concateIt = false;
      }
      this.fetchingGifs = true;
      Gify.search('gifs', {
          "q": queryText,
          offset: this.page++,
        }).then((response) => {
          this.responseData = response;
          this.stopLoader();
      })
      .catch((err) => {
        this.stopLoader();
      })
    },

    // to fetch gif trending
    getTrendingGifs () {
      if (this.currentSearchType !== 'trending') {
        this.currentSearchType = 'trending';
        this.concateIt = false;
        this.page = 0;
      } else {
        this.concateIt = true;
      }
      this.fetchingGifs = true;
      Gify.trending("gifs", {
        offset: this.page++,
      })
      .then((response) => {
        this.responseData = response;
        this.stopLoader();
      })
      .catch((err) => {
        this.stopLoader();
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .stickyContent{
    width: 100%;
    position: fixed;
    top:0;
    left: 0;
    background: #fff;
    z-index: 1000;
    box-shadow: 0px 0px 5px 5px #ccc;

  }
  .resultHeader {
    border-bottom: 1px solid #ddd;
    width: 100%;
    padding: 10px;
    margin: 0 auto;
  }
  .groups {
    overflow: auto;
    text-align: left;
    padding: 15px;
    max-height: 120px
  }
  .groups.hide{
    padding: 0px;
    height: 0px;
  }
  .groups .title{
    margin-bottom: 10px;
  }
  .groups .title.hover:hover {
    color: #479b49;
    transition: all 0.25s ease;
  }
  .scrollTopButton{
    position: fixed;
    bottom: 10px;
    right: 10px;
    font-size: 30px;
    color: rgba(0,0,0,0.5);
    cursor: pointer;
  }
  .scrollTopButton:hover{
    color: #479b49;
  }
</style>

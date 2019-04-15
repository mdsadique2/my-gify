<template>
  <div class="searchResultsContainer">
      <div class="noData" v-if="results === undefined || results.length === 0 && !loader">
        No Results found.
      </div>

      <div v-masonry transition-duration="0.3s" item-selector=".item" v-if="results.length > 0">
        <div  v-masonry-tile class="item cursorPointer" v-for="(data, index) in results" @click="showImageDetails(data, index)">
          <img
            :src="searchType === 'category' ? data.gif.images.downsized.url : data.images.downsized.url"
            :alt="searchType === 'category' ? data.gif.title : data.title"/>
          <i @click.stop="controlGif($event, data, 'play')" class="controls play far fa-play-circle"></i>
          <i @click.stop="controlGif($event, data, 'pause')" class="controls pause far fa-pause-circle"></i>
          <div class="displayFlex">
            <div class="caption width50">
              {{searchType === 'category' ? data.gif.title : data.title}}
            </div>
            <div class="width50 displayFlex share">
              <i @click.stop="shareGif()" class="fas fa-share-alt-square"></i>
            </div>
          </div>
        </div>
      </div>

      <div class="loader">
        <i class="fas fa-spinner fa-spin"></i>
      </div>
      <!-- to show image detail popUp -->
      <imageDetail :imageData="results" :currentIndex="currentImageIndex" v-if="showImageDetailsPanel" @closeFunction="closeDetailsPanel"></imageDetail>
  </div>
</template>

<script>
import Vue from 'vue'
import imageDetail from './imageDetailPanel.vue'
import {VueMasonryPlugin} from 'vue-masonry';
Vue.use(VueMasonryPlugin)

export default {
  name: 'searchResults',
  components: {
    imageDetail
  },
  props: {
    responseObj: {
      type: Object,
      default: () => {
        return {}
      }
    },
    concatResults: {
      type: Boolean,
      default: false
    },
    sizeKey: {
      type: String,
      default: 'downsized'
    },
    loader: {
      type: Boolean,
      default: false
    },
    searchType: {
      type: String,
      default: ''
    },
    searchKey: {
      type: String,
      default: ''
    },
    searchAsTextFunction: {
      type: Function,
      default: () => {}
    }
  },
  data () {
    return {
      results: [],
      currentPage: 0,
      placeholderCounts: 10,
      randomColorArray : [],
      currentImageIndex: 0,
      showImageDetailsPanel: false
    }
  },
  created () {
    this.randomColor();
  },
  methods: {
    controlGif (event, data, type) {
      var parent = event.target.parentNode;
      var img = parent.getElementsByTagName('img')[0];
      var play = parent.getElementsByClassName('play')[0];
      var pause = parent.getElementsByClassName('pause')[0];
      if (type === 'pause') {
        play.style.visibility = "visible";
        pause.style.visibility = "hidden";
        img.src = (data.images || data.gif.images).downsized_still.url;
      } else {
        play.style.visibility = "hidden";
        pause.style.visibility = "visible";
        img.src = (data.images || data.gif.images).downsized.url;
      }
    },

    tryTextSearch () {
      this.$emit('searchAsTextFunction');
    },

    shareGif () {
      alert('Share feature is not available now!')
    },

    closeDetailsPanel () {
      this.showImageDetailsPanel = false;
    },
    randomColor () {
      for (var i = 0; i < this.placeholderCounts; i++) {
        this.randomColorArray.push('#' + Math.floor(Math.random()*16777215).toString(16));
      }
    },
    updateResults (data) {
      var that = this;
      var result = data;
      if (this.concatResults === true) {
        this.results = this.results.concat(data);
        return;
      }
      this.results = [];
      setTimeout(function(){
        that.results = result;
      }, 1000)
    },
    showImageDetails (data, index) {
      this.currentImageIndex = index;
      this.showImageDetailsPanel = true;
    }
  },
  watch: {
    responseObj (newVal, oldVal) {
      this.updateResults(newVal.data);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .searchResultsContainer {
    background: #eee;
    padding: 10px 20px;
  }
  .loader {
    margin-top: 25px;
    margin-bottom: 25px;
    height: 50px;
    opacity: 0.5;
    font-size: 50px;
  }
  .item {
    /*display: inline-block;*/
    box-sizing: border-box;
    padding: 10px;
    width: 24%;
    border: 1px solid #ddd;
    margin: 10px .5%;
    background: #fff;
    border-radius: 2px;
    text-transform: capitalize;
    text-align: left;
  }
  .item img {
    width: 100%;
    height: auto;
    min-height: 80px;
  }

  .item .controls {
    color: rgba(180, 180, 180, 0.65);
    position: absolute;
    z-index: 50;
    top: 30%;
    left: 44%;
    font-size: 50px;
  }

  .item .caption {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .item .controls.play {
    visibility: hidden;
  }

  .item .share {
    justify-content: flex-end;
    color: #999;
  }

  .item .controls.pause {
    visibility: visible;
  }
  .noData {
    margin-top: 50px;
  }
  @media only screen and (max-width: 720px) {
    .item {
      width: 48%;
      margin: 10px 1%;
    }
  }

  @media only screen and (min-width: 721px) and (max-width: 1200px) {
    .item {
      width: 31%;
      margin: 10px 1.66%;
    }
  }
</style>

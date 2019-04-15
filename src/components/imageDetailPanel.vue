<template>
  <div>
    <div class="width100 imageDetailsPanel">
      <div class="imageDetailsContainer displayFlex">
        <div class="imageCard">
          <img :src='currentImageUrl' />
          <i class="navigateIcons navigateIcon--left fas fa-arrow-circle-left" @click="change('prev')"></i>
          <i class="navigateIcons navigateIcon--right fas fa-arrow-circle-right" @click="change('next')"></i>
        </div>

        <div class="otherDetails">
          <div class="titleBar positionSticky">
            {{currentImageDetails.title}}
            <i class="closePanelIcon far fa-times-circle" @click="closePanel()"></i>
          </div>
          <div>
            <br>
            <userDetail :userObject="currentImageDetails.user"></userDetail>
            <br>
            <br>

            <div class="keyVal">
              <div class="key">Short URL :</div>
              <div class="val"><a :href="currentImageDetails.bitly_url" target="_blank">{{currentImageDetails.bitly_url}}</a></div>
            </div>
            <div class="keyVal">
              <div class="key">Source URL :</div>
              <div class="val"><a :href="currentImageDetails.source" target="_blank">{{currentImageDetails.source}}</a></div>
            </div>
            <div class="keyVal">
              <div class="key">Ratings :</div>
              <div class="val">{{currentImageDetails.rating}}</div>
            </div>
          </div>
          <!-- <div><pre>{{currentImage}}</pre></div> -->
        </div>

      </div>
    </div><!-- End of myCtrl div -->
  </div>
</template>

<script>
import Gify from '../sdk/gify'
import userDetail from './userDetails.vue'

export default {
  name: 'imageDetail',
  components: {
    userDetail
  },
  props: {
    closeFunction: {
      type: Function,
      default: () => {
      }
    },
    imageData: {
      type: Array,
      default: () => {
        return []
      }
    },
    currentIndex: {
      type: Number,
      default: 0
    }
  },
  data () {
    return {
      currentImage: {},
      currentImageDetails: {},
      currentImageUrl: '',
      displayIndex: 0
    }
  },
  created () {
    this.getCurrentImageSelection();
  },
  methods: {
    getGifDetails () {
      Gify.gifByID(this.currentImage.id||this.currentImage.gif.id)
      .then((response) => {
        this.currentImageDetails = response.data
      })
      .catch((err) => {

      })
    },
    getCurrentImageSelection (index) {
      if (index === undefined) {
        this.displayIndex = this.currentIndex;
      } else {
        this.displayIndex = index;
      }
      this.currentImage = this.imageData.length > 0 ? this.imageData[this.displayIndex] : {}
      this.currentImageUrl = (this.currentImage.images || this.currentImage.gif.images).downsized_medium.url;
      this.getGifDetails();
    },
    change (dir) {
      if (dir === 'next') {
        this.displayIndex = this.displayIndex + 1;
      } else {
        this.displayIndex = this.displayIndex - 1;
      }
      this.getCurrentImageSelection(this.displayIndex);
    },
    closePanel () {
      this.$emit('closeFunction');
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  .imageDetailsPanel {
    position: fixed;
    display: flex;
    top: 0px;
    left: 0px;
    background: rgba(0,0,0,0.75);
    overflow: auto;
    height: 100vh;
    width: 100%;
    padding: 20px;
    z-index: 1000;
    box-sizing: border-box;
  }

  .imageDetailsContainer {
    width: 98%;
    height: 98%;
    background: #fff;
    border-radius: 4px;
    padding: 20px;
  }

  .imageCard {
    position: relative;
    width: 40%;
    height: 100%;
    padding: 10px 40px;
    display: flex;
    justify-content: center;
    align-items: center;
    border-right: 1px solid #ddd;
  }

  .imageCard img {
    width: 100%;
    height: auto;
  }

  .otherDetails {
    position: relative;
    width: 60%;
    height: 100%;
    overflow: auto;
    padding: 0 25px;
  }

  .otherDetails .titleBar {
    text-align: left;
    padding: 20px 0px 10px;
    margin: 5px 0px;
    text-transform: capitalize;
    border-bottom: 1px solid #ddd;
    font-size: 20px;
  }

  .closePanelIcon {
    position: absolute;
    right: 0px;
    top: -5px;
    font-size: 30px;
    color: #bbb;
    cursor: pointer;
  }
  .closePanelIcon:hover {
    color: #999;
  }
  .navigateIcons {
    position: absolute;
    top: 40%;
    font-size: 25px;
    cursor: pointer;
  }

  .navigateIcon--left {
    left: 5px;
  }
  .navigateIcon--right {
    right: 5px;
  }

  .keyVal {
    display: flex;
    text-align: left;
    font-size: 14px;
    padding-bottom: 10px;
    margin-bottom: 5px;
    border-bottom: 1px solid #eee;
  }
  .keyVal .key {
    width: 25%;
  }
  .keyVal .val {
    width: 75%;
  }
</style>

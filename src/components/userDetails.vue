<template>
  <div>
    <div v-if="userObject === null" class="textAlignCenter noData">
      No user data found
    </div>
    <div  v-if="userObject !== null" class="textAlignLeft">User Profile Snapshot:</div>
    <div class="userContainer" v-if="userObject !== null">
      <div class="width100">
        <img class="banner" :src="userObject.banner_url"/>
        <img class="profile" :src="userObject.avatar_url"/>
        <div class="id displayFlex">
          <a :href="userObject.profile_url" target="_blank">@{{userObject.username}}</a>
          <div class="snsIcons">
            <a :href="userObject.website_url" target="_blank"><i class="fas fa-globe"></i></a>
            <a :href="userObject.facebook_url" target="_blank"><i class="fab fa-facebook-square"></i></a>
            <a :href="userObject.twitter_url" target="_blank"><i class="fab fa-twitter-square"></i></a>
            <a :href="userObject.instagram_url" target="_blank"><i class="fab fa-instagram"></i></a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'userDetails',
  props: {
    userObject: {
      type: Object,
      default: () => {
        return {}
      }
    }
  },
  data () {
    return {

    }
  },
  created () {

  },
  methods: {

  },
  watch: {
    userObject (newVal, oldVal) {
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.userContainer {
  text-align: left;
  position: relative;
  padding: 20px 40px 20px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: #f5f5f5;
}
.banner {
  width: 100%;
  min-height: 124px;
  height: auto;
  border: 4px solid #ddd;
}
.profile {
  width: 100px;
  height: 100px;
  position: absolute;
  top: 100px;
  left: 70px;
  border-radius: 50%;
  border: 4px solid #ddd;
}

.userContainer .id {
  padding-left: 140px;
  padding-top: 10px;
  font-style: italic;
  color: #999;
  justify-content: space-between;
}

.snsIcons {
  width: 30%;
  display: flex;
  justify-content: space-between;
  font-size: 20px;
}

.snsIcons a {color: #999; cursor: pointer;}
.snsIcons a:hover {color: #479b49}

</style>

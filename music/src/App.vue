<template>
  <div id="app" >
    <div class="topbar"  v-if="$route.meta.showNavBar">
      <div class="topfix">
        <h1 class="topfl"></h1>
        <div class="topfr">
          <span class="topbtn">下载APP</span>
        </div>
      </div>
    </div>
    <ul id="nav" v-if="$route.meta.showNavBar">
      <li>
        <router-link to="/">推荐音乐</router-link>
      </li>
      <li>
        <router-link to="/hot">热歌榜</router-link>
      </li>
      <li>
        <router-link to="/search">搜索</router-link>
      </li>
    </ul>
    <section class="routes">
      <transition
        name="custom-classes-transition"
        enter-active-class="animate__animated animate__slideInRight"
        leave-active-class="animate__animated animate__slideOutLeft"
      >
        <router-view
          @change-current-song="changeCurrentSong"
          @change-current-play-list="changeCurrentPlayList"
          :currentSongId="currentSong ? currentSong.id : null"
          :playing="playing"
          style="
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            overflow-y: auto;
          "
        />
      </transition>
    </section>
    <audio
      ref="audio"
      :src="currentSongUrl"
      controls
      style="height: 40px; margin-bottom: 40px"
      autoplay
      @playing="playing = true"
      @pause="playing = false"
      @timeupdate="timeupdate"
      @durationchange="durationchange"
    ></audio>
    <Play
      v-if="currentSong"
      :currentSong="currentSong"
      :playing="playing"
      @toggle-playing-state="togglePlayingState"
      :currentTime="currentTime"
      :duration="duration"
      :currentPlayList="currentPlayList"
      @change-current-song="changeCurrentSong"
      @next-song="nextSong"
      @prev-song="prevSong"
      @current-time-change="$refs.audio.currentTime = $event"
    ></Play>
  </div>
</template>

<script>
import Play from "./components/Play.vue";
export default {
  components: {
    Play,
  },
  data: function () {
    return {
      currentSong: null,
      currentPlayList: [],
      playing: false,
      currentTime: 0,
      duration: 0,
    };
  },
  computed: {
    currentSongUrl: function () {
      if (this.currentSong) {
        return `https://music.163.com/song/media/outer/url?id=${this.currentSong.id}.mp3`;
      } else {
        return null;
      }
    },
  },
  methods: {
    changeCurrentSong: function (song) {
      // console.log(song);
      this.currentSong = song;
    },
    changeCurrentPlayList: function (list) {
      this.currentPlayList = list;
    },
    togglePlayingState: function () {
      if (this.playing) {
        // 暂停歌曲
        this.$refs.audio.pause();
      } else {
        // 播放歌曲
        this.$refs.audio.play();
      }
    },
    timeupdate: function (event) {
      this.currentTime = event.target.currentTime;
    },
    durationchange: function (event) {
      this.duration = event.target.duration;
    },
    nextSong: function () {
      console.log(123);
      var index = this.currentPlayList.findIndex((item) => {
        return item.id === this.currentSong.id;
      });
      index++;
      index = index >= this.currentPlayList.length - 1 ? 0 : index;
      index = index <= 0 ? this.currentPlayList.length - 1 : index;
      // console.log(index);
      this.changeCurrentSong(this.currentPlayList[index]);
    },
    prevSong: function () {
      console.log(456);
      var index = this.currentPlayList.findIndex((item) => {
        return item.id === this.currentSong.id;
      });
      index--;
      index = index >= this.currentPlayList.length - 1 ? 0 : index;
      index = index <= 0 ? this.currentPlayList.length - 1 : index;
      // console.log(index);
      this.changeCurrentSong(this.currentPlayList[index]);
    },
  },
};
</script>

<style lang="less">
.animate__animated {
  animation-duration: 0.3s;
}
@keyframes rotate {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  // text-align: center;
  color: #2c3e50;
}

.topbar {
  width: 100%;
  height: 0;
  padding-bottom: 84px;
  transition: padding-bottom 0.3s;
  position: relative;
  .topfix {
// position: fixed;
    // top: 0;
    // left: 0;
    display: flex;
    -webkit-box-pack: justify;
    justify-content: space-between;
    -webkit-box-align: center;
    -ms-flex-align: center;
    align-items: center;
    padding: 0 10px;
    width: 100%;
    height: 84px;
    background-color: #d43c33;
    box-sizing: border-box;
    .topfl {
      position: relative;
      width: 140px;
      height: 25px;
      background-image: url("./assets/wyymusic1.png");
      background-size: contain;
    }
    .topfr {
      border: 1px;
      .topbtn {
        position: relative;
        display: inline-block;
        height: 36px;
        line-height: 36px;
        font-size: 16px;
        width: 100px;
        text-align: center;
        color: #d43c33;
        background-color: #fff;
        border-radius: 20px;
        // .topbtn::after {
        //   border-color: #fff;
        //   border-radius: 20px;
        // }
      }
    }
  }
}

#nav {
  // padding: 30px;
  display: flex;
  // height: 50px;
  // border-bottom: 1px solid blue;
  box-shadow: 0 -1px 3px 0px rgb(231, 231, 231) inset;

  li {
    flex: 1;
    text-align: center;

    // background-color: lightblue;
    a {
      // font-weight: bold;
      color: #2c3e50;
      line-height: 40px;
      display: inline-block;
      text-decoration: none;
      font-size: 15px;
      padding: 0 5px;

      &.router-link-exact-active {
        color: #d43c33;
        border-bottom: 2px solid #d43c33;
      }
    }
  }
}
.routes {
  display: block;
  position: relative;
  top: 0;
  left: 0;
  height: calc(100vh - 42px);
  overflow: hidden;
}

// xxx.apk => xxx.zip =>解压
</style>

<template>
  <div class="hot">
    <div class="hottop">
      <div class="hotopct">
        <div class="hotfont"></div>
        <div class="hottime">更新日期：{{ time }}</div>
      </div>
    </div>
    <ul class="songs">
      <SongListItem
        v-for="(item, index) in detail.tracks"
        :key="item.id"
        :item="item"
        @change-current-song="
          $emit('change-current-song', $event);
          $emit('change-current-play-list', detail.tracks);
        "
        :currentSongId="currentSongId"
        :playing="playing"
        :class="{ lt3: index < 3 }"
        >{{ index + 1 }}
      </SongListItem>
    </ul>
    <div class="hotdn">
      <span class="hotview">查看完整歌单</span>
    </div>
  </div>
</template>

<script>
import SongListItem from "@/components/SongListItem.vue";
export default {
  data: function () {
    return {
      time: "",
      detail: null,
    };
  },
  props: {
    currentSongId: {
      type: Number,
    },
    playing: Boolean,
  },
  components: {
    SongListItem,
  },
  methods: {
    // opensongs(){
    //   this.songs="!overflow: hidden;"
    // },
    getTime: function () {
      let mm =
        new Date().getMonth() + 1 < 10
          ? "0" + (new Date().getMonth() + 1)
          : new Date().getMonth() + 1;
      let dd =
        new Date().getDate() < 10
          ? "0" + new Date().getDate()
          : new Date().getDate();
      this.time = mm + "月" + dd + " 日";
    },
  },
  created: function () {
    this.axios
      .get("http://apis.netstart.cn/music//playlist/detail?id=3778678", {})
      .then((res) => {
        console.log(this, res);
        this.detail = res.data.playlist;
      });
    this.getTime();
  },
};
</script>

<style lang="less">
.hottop {
  padding-top: 38.9%;
  overflow: hidden;
  background: url("https://s3.music.126.net/mobile-new/img/hot_music_bg_2x.jpg?f01a252389c26bcf016816242eaa6aee=")
    no-repeat;
  background-size: contain;
  // background-color: rgba(0, 0, 0, 0.2);
  position: relative;
  &.hottop::before {
  content: " ";
  position: absolute;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.2);
}
  .hotopct {
    width: 100%;
    height: 140px !important;
    display: flex;
    padding-left: 20px;
    position: absolute;
    left: 0;
    top: 0;
    flex-direction: column;
    justify-content: center;
    .hotfont {
      width: 142px;
      height: 67px;
      background: url("https://s3.music.126.net/mobile-new/img/index_icon_2x.png?5207a28c3767992ca4bb6d4887c74880=")
        no-repeat -24px -30px;
      background-size: 166px 97px;
    }
    .hottime {
      margin-top: 10px;
      color: hsla(0, 0%, 100%, 0.8);
      font-size: 12px;
    }
  }
}
.hotdn {
  height: 55px;
  line-height: 55px;
  text-align: center;
  .hotview {
    display: inline-block;
    color: #999;
    padding-right: 14px;
    background: url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAxNCAyMiI+PHBhdGggZmlsbD0ibm9uZSIgc3Ryb2tlPSIjY2NjIiBzdHJva2Utd2lkdGg9IjIiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTTEgMWwxMCAxMEwxIDIxIi8+PC9zdmc+)
      100% no-repeat;
    background-size: 7px 12px;
  }
}

.songs {
  height: 1050px;
  overflow: hidden;
}
</style>

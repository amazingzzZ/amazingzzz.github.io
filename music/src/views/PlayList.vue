<template>
  <div class="all" v-if="detail">
    <div class="header">
      <button class="btn" @click="comeback">返回</button>
      <div
        class="mask"
        :style="{
          backgroundImage: `url(${detail.coverImgUrl}?imageView-1&type=webp&thumbnail=246x0)`,
        }"
      ></div>

      <div class="wrap">
        <img
          class="photo"
          :src="`${detail.coverImgUrl}?imageView=1&type=webp&thumbnail=246x0`"
          alt=""
        />
        <!-- <span class="bofang">
          <img
            class="num"
            style="width: 10px"
            src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyMiAyMCI+PHBhdGggZmlsbC1ydWxlPSJldmVub2RkIiBmaWxsPSIjMDQwMDAwIiBkPSJNMjIgMTYuNzc3YzAgMS4yMzMtMS4xMjEgMi4yMzMtMi41MDYgMi4yMzMtMS4zODQgMC0yLjUwNi0xLTIuNTA2LTIuMjMzdi0yLjU1M2MwLTEuMjM0IDEuMTIyLTIuMjMzIDIuNTA2LTIuMjMzLjE3NCAwIC4zNDMuMDE3LjUwNi4wNDZ2LTEuMzdoLS4wMzNjLjAxNy0uMjIuMDMzLS40NDEuMDMzLS42NjZhOCA4IDAgMCAwLTE2IDBjMCAuMjI1LjAxNi40NDYuMDM0LjY2Nkg0djEuMzdjLjE2My0uMDI5LjMzMy0uMDQ2LjUwNS0uMDQ2IDEuMzg0IDAgMi41MDYuOTk5IDIuNTA2IDIuMjMzdjIuNTUzYzAgMS4yMzMtMS4xMjIgMi4yMzMtMi41MDYgMi4yMzNTMiAxOC4wMTEgMiAxNi43Nzd2LTIuNTUzYzAtLjI1OC4wNTktLjUwMS4xNDgtLjczQS45ODIuOTgyIDAgMCAxIDIgMTMuMDAxdi0yLjY2N2MwLS4wMjMuMDEyLS4wNDMuMDEzLS4wNjctLjAwNC0uMDg4LS4wMTMtLjE3Ni0uMDEzLS4yNjYgMC01LjUyMyA0LjQ3Ny0xMCAxMC0xMHMxMCA0LjQ3NyAxMCAxMGMwIC4wOS0uMDA5LjE3OC0uMDE0LjI2Ni4wMDIuMDI0LjAxNC4wNDQuMDE0LjA2N3YyYS45ODguOTg4IDAgMCAxLS4zNi43NTNjLjIyNC4zMzQuMzYuNzIuMzYgMS4xMzh2Mi41NTIiIG9wYWNpdHk9Ii4xNSIvPjxwYXRoIGZpbGwtcnVsZT0iZXZlbm9kZCIgZmlsbD0iI2ZmZiIgZD0iTTIwIDE2Ljc3N2MwIDEuMjMzLTEuMTIxIDIuMjMzLTIuNTA2IDIuMjMzLTEuMzg0IDAtMi41MDYtMS0yLjUwNi0yLjIzM3YtMi41NTNjMC0xLjIzNCAxLjEyMi0yLjIzMyAyLjUwNi0yLjIzMy4xNzQgMCAuMzQzLjAxNy41MDYuMDQ2di0xLjM3aC0uMDMzYy4wMTctLjIyLjAzMy0uNDQxLjAzMy0uNjY2YTggOCAwIDAgMC0xNiAwYzAgLjIyNS4wMTYuNDQ2LjAzNC42NjZIMnYxLjM3Yy4xNjMtLjAyOS4zMzMtLjA0Ni41MDUtLjA0NiAxLjM4NCAwIDIuNTA2Ljk5OSAyLjUwNiAyLjIzM3YyLjU1M2MwIDEuMjMzLTEuMTIyIDIuMjMzLTIuNTA2IDIuMjMzUzAgMTguMDExIDAgMTYuNzc3di0yLjU1M2MwLS4yNTguMDU5LS41MDEuMTQ4LS43M0EuOTgyLjk4MiAwIDAgMSAwIDEzLjAwMXYtMi42NjdjMC0uMDIzLjAxMi0uMDQzLjAxMy0uMDY3LS4wMDQtLjA4OC0uMDEzLS4xNzYtLjAxMy0uMjY2IDAtNS41MjMgNC40NzctMTAgMTAtMTBzMTAgNC40NzcgMTAgMTBjMCAuMDktLjAwOS4xNzgtLjAxNC4yNjYuMDAyLjAyNC4wMTQuMDQ0LjAxNC4wNjd2MmEuOTg4Ljk4OCAwIDAgMS0uMzYuNzUzYy4yMjQuMzM0LjM2LjcyLjM2IDEuMTM4djIuNTUyIi8+PC9zdmc+"
            alt=""
          />
        </span> -->
        <div class="plhead" style="margin-left: 16px">
          <h2
            style="
              padding-top: 1px;
              font-size: 17px;
              line-height: 1.3;
              color: #fefefe;
              height: 44px;
              display: -webkit-box;
              -webkit-box-pack: center;
            "
          >
            {{ detail.name }}
          </h2>
          <div class="auth" style="color: hsla(0, 0%, 100%, 0.7)">
            <img
              style="
                width: 30px;
                vertical-align: middle;
                border-radius: 50%;
                height: 30px;
              "
              :src="detail.creator.avatarUrl"
              alt=""
            />
            {{ detail.creator.nickname }}
          </div>
        </div>
      </div>
    </div>
    <ul>
      <!-- <li v-for="item in detail.tracks" :key="item.id">{{ item.name }}</li> -->
      <SongListItem
        v-for="(item, index) in detail.tracks"
        :key="item.id"
        :item="item"
        @change-current-song="
          $emit('change-current-song', item);
          $emit('change-current-play-list', detail.tracks);
        "
        :currentSongId="currentSongId"
        :playing="playing"
        :class="{ lt3: index < 3 }"
        >{{ index + 1 }}
      </SongListItem>
    </ul>
  </div>
</template>

<script>
import SongListItem from "@/components/SongListItem.vue";
export default {
  components: {
    SongListItem,
  },
  props: {
    currentSongId: {
      type: Number,
    },
    playing: Boolean,
  },
  data: function () {
    return {
      detail: null,
    };
  },
  methods: {
    comeback(){
      this.$router.go(-1);
    },
    getPlayListDetail: function (id) {
      this.axios
        .get("http://apis.netstart.cn/music/playlist/detail", {
          params: {
            id,
          },
        })
        .then((res) => {
          console.log(res);
          this.detail = res.data.playlist;
        });
    },
  },

  created: function () {
    this.getPlayListDetail(this.$route.query.id);
  },
};
</script>

<style lang="less">
.all {
  .header {
    position: relative;
    padding: 30px 10px 30px 15px;
    position: relative;
    overflow: hidden;
    .btn {
      position: absolute;
      top: 0;
      left: 0;
      width: 12vw;
      height: 4vh;
    }
    .mask {
      position: absolute;
      left: 0;
      top: 0;
      right: 0;
      bottom: 0;
      background-repeat: no-repeat;
      background-size: cover;
      background-position: 50%;
      filter: blur(20px);
      transform: scale(1.5);
    }
    .mask::after {
      content: " ";
      background-color: rgba(0, 0, 0, 0.25);
      position: absolute;
      left: 0;
      top: 0;
      right: 0;
      bottom: 0;
    }
    .wrap {
      display: flex;
      position: relative;
      .photo {
        width: 126px;
        height: 126px;
        vertical-align: middle;
        background-color: #e2e2e3;
      }
      span {
        position: absolute;
        top: 3px;
        right: 3px;
        font-size: 10px;
        color: white;
        .num {
        }
      }
      // .plhead {
      //   flex: 1 1 auto;
      //   width: 1%;
      //   margin-left: 16px;
      //   padding-top: 3px;
      //   .title {
      //     padding-top: 1px;
      //     font-size: 17px;
      //     line-height: 1.3;
      //     color: #fefefe;
      //     height: 44px;
      //     display: -webkit-box;
      //     -webkit-box-pack: center;
      //   }
      // }
    }
  }
}
</style>

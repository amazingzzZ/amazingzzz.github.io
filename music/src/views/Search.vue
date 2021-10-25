<template>
  <div class="search" @scroll="scroll">
    <div class="searchbox">
      <div class="inputover">
        <i class="inputlogo"> </i>
        <input
          type="text"
          placeholder="搜索歌曲、歌手、专辑"
          v-model.trim="value"
          @keyup.enter="value && (searching = true)"
          @focus="inputing = true"
          @blur="inputing = false"
        />
        <!-- <label class="holder">搜索歌曲、歌手、专辑</label> -->
        <div class="close">
          <span class="empty"></span>
        </div>
      </div>
    </div>

    <section class="hots" v-if="!value && !searching">
      <h5 class="title">热门搜索</h5>
      <ul>
        <li
          class="item"
          v-for="hot in hots"
          :key="hot.first"
          @click="
            searching = true;
            value = hot.first;
          "
        >
          {{ hot.first }}
        </li>
      </ul>
      <ol class="history">
        <li class="item" v-for="(h, index) in history" :key="h">
          <i class="hislogo"></i>
          <div class="histype">
            <span
              class="link"
              @click="
                searching = true;
                value = h;
              "
              >{{ h }}</span
            >
            <div class="close" @click="deleteHistory(index)">
              <span class="empty"></span>
            </div>
          </div>
        </li>
      </ol>
    </section>

    <section class="suggests" v-if="value && !searching">
      <h5 class="suggesttitle">搜索建议</h5>
      <ul>
        <li
          class="recomitem"
          v-for="(item, index) in suggests"
          :key="index"
          @click="
            searching = true;
            value = item.keyword;
          "
        >
          <i class="searchlogo"></i> {{ item.keyword }}
        </li>
      </ul>
    </section>

    <section class="results" v-if="searching">
      <h5 style="font-size: 15px; color: #507daf">搜索结果</h5>
      <ul>
        <SongListItem
          v-for="(item, index) in searchResults"
          :key="index"
          :item="item"
          @change-current-song="
            $emit('change-current-song', $event);
            $emit('change-current-play-list', searchResults);
          "
          :currentSongId="currentSongId"
          :playing="playing"
        />
        <!-- {{ item.name }} -->
      </ul>
      <p v-if="!hasMore" class="mowei">没有更多了</p>
    </section>
  </div>
</template>

<script>
import debounce from "@/assets/debounce";
import SongListItem from "@/components/SongListItem.vue";
export default {
  components: {
    SongListItem,
  },
  data: function () {
    return {
      hots: [],
      suggests: [],
      searchResults: [],
      value: "",
      searching: false,
      inputing: false,
      page: 0,
      hasMore: false,
      history: JSON.parse(window.localStorage.getItem("history")) || [],
      newsongs: [],
    };
  },
  props: {
    currentSongId: {
      type: Number,
    },
    playing: Boolean,
  },
  methods: {
    scroll: function (event) {
      //   console.log(event);
      if (this.hasMore) {
        if (
          event.target.offsetHeight + event.target.scrollTop ===
          event.target.scrollHeight
        ) {
          console.log("触底");

          this.getSearch();
        }
      } else {
        console.log("没有更多了");
      }

      //   document.querySelector(".search").scrollHeight;
      //   document.querySelector(".search").scrollTop;
      //   document.querySelector(".search").offsetHeight;
    },
    getSearch: function () {
      this.axios
        .get("http://apis.netstart.cn/music/search", {
          params: {
            keywords: this.value,
            limit: 30,
            offset: this.page * 30,
          },
        })
        .then((res) => {
          this.searchResults.push(...res.data.result.songs);
          this.page++;
          this.hasMore = res.data.result.hasMore;
        });

      // 记录一下
      // this.history.push(this.value)
      this.history = [...new Set([...this.history, this.value])];
      window.localStorage.setItem("history", JSON.stringify(this.history));
    },
    deleteHistory: function (index) {
      this.history.splice(index, 1);
      window.localStorage.setItem("history", JSON.stringify(this.history));
    },
  },
  created: function () {
    this.axios.get("http://apis.netstart.cn/music/search/hot").then((res) => {
      this.hots = res.data.result.hots;
    });
    this.axios
      .get("http://apis.netstart.cn/music/personalized/newsong")
      .then((res) => {
        // console.log(this, res);
        this.newsongs = res.data.result;
      });
  },
  changeSeletc: debounce(function () {
    console.log(this.input);
  }, 500),
  watch: {
    value: function (n) {
      if (this.inputing) {
        this.searching = false;
      }
      if (n && !this.searching) {
        this.axios
          .get("http://apis.netstart.cn/music/search/suggest", {
            params: {
              keywords: n,
              type: "mobile",
            },
          })
          .then((res) => {
            this.suggests = res.data.result.allMatch;
          });
      } else {
        this.suggests = [];
      }
    },

    searching: function (n) {
      if (n && this.value) {
        this.getSearch();
      } else {
        this.searchResults = [];
      }
    },
  },
};
</script>

<style lang="less">
.search {
  // padding: 10px;
  .searchbox {
    padding: 15px 10px;
    position: relative;
    .inputover {
      position: relative;
      width: 100%;
      height: 30px;
      padding: 0 30px;
      box-sizing: border-box;
      background: #ebecec;
      border-radius: 30px;
      .inputlogo {
        position: absolute;
        left: 0;
        top: 9px;
        margin: 0 8px;
        vertical-align: middle;
        width: 13px;
        height: 13px;
        background-image: url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNiAyNiI+PHBhdGggZmlsbC1ydWxlPSJldmVub2RkIiBmaWxsPSIjYzljOWNhIiBkPSJNMjUuMTgxIDIzLjUzNWwtMS40MTQgMS40MTQtNy4zMTUtNy4zMTRBOS45NjYgOS45NjYgMCAwIDEgMTAgMjBDNC40NzcgMjAgMCAxNS41MjMgMCAxMFM0LjQ3NyAwIDEwIDBzMTAgNC40NzcgMTAgMTBjMCAyLjM0Mi0uODExIDQuNDktMi4xNiA2LjE5NWw3LjM0MSA3LjM0ek0xMCAyYTggOCAwIDEgMCAwIDE2IDggOCAwIDAgMCAwLTE2eiIvPjwvc3ZnPg==);
      }
      input {
        width: 100%;
        height: 30px;
        line-height: 18px;
        background: rgba(0, 0, 0, 0);
        font-size: 14px;
        color: #333;
        -webkit-appearance: none;
        border-radius: 0;
        border: 0;
        background-color: rgba(0, 0, 0, 0);
      }
      input:focus {
        outline: none;
      }
      .holder {
        position: absolute;
        left: 30px;
        top: 5px;
        font-size: 14px;
        color: #c9c9c9;
        background: rgba(0, 0, 0, 0);
        pointer-events: none;
        cursor: default;
      }
      .close {
        position: absolute;
        right: 0;
        top: 0;
        width: 30px;
        height: 30px;
        line-height: 28px;
        text-align: center;
        .empty {
          display: none;
          vertical-align: middle;
          width: 14px;
          height: 14px;
          background-image: url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyOCAyOCI+PGcgZmlsbC1ydWxlPSJldmVub2RkIj48cGF0aCBmaWxsPSIjYmNiZGJkIiBkPSJNMTQgMGM3LjczMiAwIDE0IDYuMjY4IDE0IDE0cy02LjI2OCAxNC0xNCAxNFMwIDIxLjczMiAwIDE0IDYuMjY4IDAgMTQgMHoiLz48cGF0aCBkPSJNMTkgOUw5IDE5TTkgOWwxMCAxMCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjZWJlY2ViIiBzdHJva2Utd2lkdGg9IjIuNSIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIi8+PC9nPjwvc3ZnPg==);
        }
      }
    }
  }
  .searchbox::after {
    width: 300%;
    height: 300%;
    transform: scale(0.333333);
    position: absolute;
    content: "";
    top: 0;
    left: 0;
    pointer-events: none;
    box-sizing: border-box;
    transform-origin: top left;
    border: 0 solid rgba(0, 0, 0, 0.1);
    border-bottom-width: 1px;
  }
  .hots {
    padding: 15px 10px 0 10px;
    .title {
      font-size: 12px;
      line-height: 12px;
      color: #666;
    }
    ul {
      overflow: hidden;
      margin: 10px 0 7px;
      li.item {
        display: inline-block;
        height: 32px;
        margin-right: 8px;
        margin-bottom: 8px;
        padding: 0 14px;
        font-size: 14px;
        line-height: 32px;
        color: #333;
        position: relative;
      }
      li.item::after {
        border-radius: 60px;
        border-color: #d3d4da;
        width: 300%;
        height: 300%;
        transform: scale(0.333333);
        position: absolute;
        content: "";
        top: 0;
        left: 0;
        pointer-events: none;
        box-sizing: border-box;
        transform-origin: top left;
        border: 1px solid rgba(0, 0, 0, 0.1);
      }
    }
    .history {
      .item {
        display: flex;
        -webkit-box-align: center;
        align-items: center;
        height: 45px;
        .hislogo {
          margin: 0 10px;
          width: 15px;
          height: 15px;
          background-image: url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAzMCAzMCI+PHBhdGggZmlsbC1ydWxlPSJldmVub2RkIiBmaWxsPSIjYzljYWNhIiBkPSJNMTUgMzBDNi43MTYgMzAgMCAyMy4yODQgMCAxNVM2LjcxNiAwIDE1IDBzMTUgNi43MTYgMTUgMTUtNi43MTYgMTUtMTUgMTVtMC0yOEM3LjgyIDIgMiA3LjgyIDIgMTVzNS44MiAxMyAxMyAxMyAxMy01LjgyIDEzLTEzUzIyLjE4IDIgMTUgMm03IDE2aC04YTEgMSAwIDAgMS0xLTFWN2ExIDEgMCAxIDEgMiAwdjloN2ExIDEgMCAxIDEgMCAyIi8+PC9zdmc+);
          vertical-align: middle;
          background-position: 0 0;
          background-size: contain;
          background-repeat: no-repeat;
        }
        .histype {
          -webkit-box-flex: 1;
          flex: 1;
          width: 1%;
          display: flex;
          -webkit-box-align: center;
          -webkit-align-items: center;
          -ms-flex-align: center;
          align-items: center;
          height: 45px;
          position: relative;
          .link {
            margin-right: 10px;
            -webkit-box-flex: 1;
            -webkit-flex: 1;
            -ms-flex: 1;
            flex: 1;
            width: 1%;
          }
          .close {
            -webkit-box-flex: 0;
            flex: 0 0 auto;
            width: 32px;
            height: 32px;
            line-height: 32px;
            .empty {
              display: inline-block;
              margin-left: 2px;
              width: 12px;
              height: 12px;
              background-image: url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI+PHBhdGggZmlsbC1ydWxlPSJldmVub2RkIiBmaWxsPSIjOTk5ODk5IiBkPSJNMTMuMzc5IDEybDEwLjMzOCAxMC4zMzdhLjk3NS45NzUgMCAxIDEtMS4zNzggMS4zNzlMMTIuMDAxIDEzLjM3OCAxLjY2MyAyMy43MTZhLjk3NC45NzQgMCAxIDEtMS4zNzgtMS4zNzlMMTAuNjIzIDEyIC4yODUgMS42NjJBLjk3NC45NzQgMCAxIDEgMS42NjMuMjg0bDEwLjMzOCAxMC4zMzhMMjIuMzM5LjI4NGEuOTc0Ljk3NCAwIDEgMSAxLjM3OCAxLjM3OEwxMy4zNzkgMTIiLz48L3N2Zz4=);
            }
          }
        }
        .histype::after {
          border-color: rgba(0, 0, 0, 0.1);
          width: 300%;
          height: 300%;
          transform: scale(0.333333);
          position: absolute;
          content: "";
          top: 0;
          left: 0;
          pointer-events: none;
          box-sizing: border-box;
          transform-origin: top left;
          border-bottom: 1px solid rgba(0, 0, 0, 0.1);
        }
      }
    }
  }
  .mowei {
    line-height: 30px;
    color: #888;
    font-size: 14px;
    text-align: center;
  }
}
.suggests {
  .suggesttitle {
    height: 50px;
    margin-left: 10px;
    padding-right: 10px;
    font-size: 15px;
    line-height: 50px;
    color: #507daf;
    position: relative;
  }
  .suggesttitle::after {
    width: 300%;
    height: 300%;
    transform: scale(0.333333);
    position: absolute;
    content: "";
    top: 0;
    left: 0;
    pointer-events: none;
    box-sizing: border-box;
    transform-origin: top left;
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
    border-bottom-width: 1px;
  }
  .recomitem {
    display: flex;
    -webkit-box-align: center;
    align-items: center;
    height: 45px;
    padding-left: 10px;
    .searchlogo {
      flex: 0 0 auto;
      margin-right: 7px;
      width: 15px;
      height: 15px;
      background-image: url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAzMCAzMCI+PHBhdGggZmlsbC1ydWxlPSJldmVub2RkIiBmaWxsPSIjMDQwMDAwIiBkPSJNMjguMTgxIDI3LjUzNWwtMS40MTQgMS40MTQtNy43NTUtNy43NTRBMTEuNDQ1IDExLjQ0NSAwIDAgMSAxMS41IDI0QzUuMTQ5IDI0IDAgMTguODUyIDAgMTIuNSAwIDYuMTQ5IDUuMTQ5IDEgMTEuNSAxIDE3Ljg1MiAxIDIzIDYuMTQ5IDIzIDEyLjVjMCAyLjc1Ni0uOTczIDUuMjg1LTIuNTg5IDcuMjY2bDcuNzcgNy43Njl6TTExLjUgM2E5LjUgOS41IDAgMSAwIDAgMTkgOS41IDkuNSAwIDAgMCAwLTE5eiIgb3BhY2l0eT0iLjMiLz48L3N2Zz4=);
    }
  }
}
</style>
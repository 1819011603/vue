<template>
  <div class="result-container">
    <div class="title-wrap">
      <h2 class="title">{{query}}</h2>
      <span class="sub-title">找到{{total}}个结果</span>
    </div>
    <el-tabs v-model="activeIndex">
      <el-tab-pane label="歌曲" name="songs">
        <table class="el-table">
          <thead>
            <th></th>
            <th>音乐标题</th>
            <th>歌手</th>
            <th>专辑</th>
            <th>时长</th>
          </thead>
          <tbody>
            <tr v-for="(item,index) in results" :key="index" class="el-table__row">
              <td>{{index+1}}</td>
              <td>
                <div class="song-wrap">
                  <div class="name-wrap" @click="play(item.id)">
                    <span>{{item.name}}</span>
                    <span class="iconfont icon-mv" v-show="item.mvid"></span>
                  </div>
                  <span>{{item.alias[0]}}</span>
                </div>
              </td>
              <td>{{item.artists[0].name}}</td>
              <td>{{item.album.name}}</td>
              <td>{{item.duration}}</td>
            </tr>
          </tbody>
        </table>
      </el-tab-pane>
      <el-tab-pane label="歌单" name="lists">
        <div class="items">
          <div class="item" v-for="(item,index) in list" :key="index">
            <div class="img-wrap">
              <div class="num-wrap">
                播放量:
                <span
                  class="num"
                >{{item.playCount > 100000? Math.round(item.playCount/10000)+'万':item.playCount}}</span>
              </div>
              <img :src="item.coverImgUrl" alt />
              <span class="iconfont icon-play"></span>
            </div>
            <p class="name">{{item.name}}</p>
          </div>
        </div>
      </el-tab-pane>
      <el-tab-pane label="MV" name="mv">
        <div class="items mv">
          <div class="item" v-for="(item,index) in mv" :key="index" @click="toMV(item.id)">
            <div class="img-wrap">
              <img :src="item.cover" alt />
              <span class="iconfont icon-play"></span>
              <div class="num-wrap">
                <div class="iconfont icon-play"></div>
                <div
                  class="num"
                >{{item.playCount > 100000? Math.round(item.playCount/10000)+'万':item.playCount}}</div>
              </div>
              <span class="time">{{item.duration}}</span>
            </div>
            <div class="info-wrap">
              <div class="name">{{item.name}}</div>
              <div class="singer">{{item.artistName}}</div>
            </div>
          </div>
        </div>
      </el-tab-pane>
    </el-tabs>
    <el-pagination
      @current-change="handleCurrentChange"
      background
      layout="prev, pager, next"
      :total="total"
      :current-page="page"
      :page-size="limit"
    ></el-pagination>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "result",

  created() {
    this.query = this.$route.query.q;
    this.Songs();
  },
  data() {
    return {
      activeIndex: "songs",
      query: "",
      limit: 30,
      offset: 0,
      type: 1,
      results: [],
      total: 20,
      page: 1,
      list: [],
      mv: [],
    };
  },
  watch: {
    activeIndex() {
      this.page = 1;
      if (this.activeIndex == "songs") {
        this.type = 1;
        this.limit = 30;
        this.Songs();
      } else if (this.activeIndex == "lists") {
        this.type = 1000;
        this.limit = 5;
        this.Lists();
      } else {
        this.type = 1004;
        this.limit = 8;
        this.Mvs();
      }
    },
  },
  methods: {
    toMV(id) {
      this.$router.push("/mv?id=" + id);
    },
    Mvs() {
      axios({
        url: "https://autumnfish.cn/search",
        method: "get",
        params: {
          keywords: this.query,
          limit: this.limit,
          offset: (this.page - 1) * this.limit,
          type: this.type,
        },
      }).then((res) => {
        console.log(res);
        this.mv = res.data.result.mvs;
        if (res.data.result.mvCount) {
          this.total = res.data.result.mvCount;
        }
        for (let i = 0; i < this.mv.length; i++) {
          let duration = parseInt(this.mv[i].duration / 1000);
          let m = parseInt(duration / 60);
          if (m < 10) m = "0" + m;
          let n = duration % 60;
          if (n < 10) n = "0" + n;
          this.mv[i].duration = `${m}:${n}`;
        }
      });
    },
    Lists() {
      axios({
        url: "https://autumnfish.cn/search",
        method: "get",
        params: {
          keywords: this.query,
          limit: this.limit,
          offset: (this.page - 1) * this.limit,
          type: this.type,
        },
      }).then((res) => {
        // console.log(res)
        this.list = res.data.result.playlists;
        if (res.data.result.playlistCount) {
          this.total = res.data.result.playlistCount;
        }
      });
    },
    play(id) {
      axios.get("https://autumnfish.cn/song/url?id=" + id).then((res) => {
        this.$parent.musicUrl = res.data.data[0].url;
      });
    },
    handleCurrentChange(val) {
      this.page = val;
      if (this.activeIndex == "songs") {
        this.Songs();
      } else if (this.activeIndex == "lists") {
        this.Lists();
      } else {
        this.Mvs();
      }
    },
    Songs() {
      axios({
        url: "https://autumnfish.cn/search",
        method: "get",
        params: {
          keywords: this.query,
          limit: this.limit,
          offset: (this.page - 1) * this.limit,
          type: this.type,
        },
      }).then((res) => {
        this.results = res.data.result.songs;
        if (res.data.result.songCount) this.total = res.data.result.songCount;
        for (let j = 0; j < this.results.length; j++) {
          let name = this.results[j].artists;
          let duration = parseInt(this.results[j].duration / 1000);
          let m = parseInt(duration / 60);
          if (m < 10) m = "0" + m;
          let s = duration % 60;
          if (s < 10) s = "0" + s;
          this.results[j].duration = `${m}:${s}`;

          if (name.length == 1) continue;
          let names = name[0].name;
          for (let i = 1; i < name.length; i++) {
            names = names + "&" + name[i].name;
          }

          name[0].name = names;
        }
      });
    },
  },
};
</script>

<style >
</style>

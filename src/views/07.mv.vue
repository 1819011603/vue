<template>
  <div class="mv-container">
    <div class="mv-wrap">
      <h3 class="title">mv详情</h3>
      <!-- mv -->
      <div class="video-wrap">
        <video controls :src="src"></video>
      </div>
      <!-- mv信息 -->
      <div class="info-wrap">
        <div class="singer-info">
          <div class="avatar-wrap">
            <img :src="details.cover" alt />
          </div>
          <span class="name">{{details.artistName}}</span>
        </div>
        <div class="mv-info">
          <h2 class="title">{{details.name}}</h2>
          <span class="date">发布：{{details.publishTime}}</span>
          <span class="number">播放：{{details.playCount}}次</span>
          <p class="desc">{{details.briefDesc}}</p>
        </div>
      </div>
      <!-- 精彩评论 -->
      <div class="comment-wrap">
        <p class="title">
          精彩评论
          <span class="number">({{hotComments.length}})</span>
        </p>
        <div class="comments-wrap">
          <div class="item" v-for="(item,index) in hotComments" :key="index">
            <div class="icon-wrap">
              <img :src="item.user.avatarUrl" alt />
            </div>
            <div class="content-wrap">
              <div class="content">
                <span class="name">{{item.user.nickname}}：</span>
                <span class="comment">{{item.content}}</span>
              </div>
              <div class="re-content" v-if="item.beReplied.length!=0">
                <span class="name">{{item.beReplied[0].nickname}}：</span>
                <span class="comment">{{item.beReplied[0].content}}</span>
              </div>
              <div class="date">{{item.time}}</div>
            </div>
          </div>
        </div>
      </div>
      <!-- 最新评论 -->
      <div class="comment-wrap">
        <p class="title">
          最新评论
          <span class="number">({{comments.length}})</span>
        </p>
        <div class="comments-wrap">
          <div class="item" v-for="(item,index) in comments" :key="index">
            <div class="icon-wrap">
              <img :src="item.user.avatarUrl" alt />
            </div>
            <div class="content-wrap">
              <div class="content">
                <span class="name">{{item.user.nickname}}：</span>
                <span class="comment">{{item.content}}</span>
              </div>
              <div class="re-content" v-if="item.beReplied.length!=0">
                <span class="name">{{item.beReplied[0].nickname}}：</span>
                <span class="comment">{{item.beReplied[0].content}}</span>
              </div>
              <div class="date">{{item.time}}</div>
            </div>
          </div>
        </div>
      </div>
      <!-- 分页器 -->
      <el-pagination
        @current-change="handleCurrentChange"
        background
        layout="prev, pager, next"
        :total="total"
        :current-page="page"
        :page-size="limit"
        :limit="limit"
      ></el-pagination>
    </div>
    <div class="mv-recommend">
      <h3 class="title">相关推荐</h3>
      <div class="mvs">
        <div class="items">
          <div class="item" v-for="(item,index) in mvs" :key="index" @click="toMV(item.id)">
            <div class="img-wrap">
              <img :src="item.cover" alt />
              <span class="iconfont icon-play"></span>
              <div class="num-wrap">
                <div class="iconfont icon-play"></div>
                <div class="num">{{item.playCount}}</div>
              </div>
              <span class="time">02:43</span>
            </div>
            <div class="info-wrap">
              <div class="name">{{item.name}}</div>
              <div class="singer">{{item.artists[0].name}}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "mv",
  created() {
    this.list();
    this.update();
    this.detail();
    this.comment();
  },
  data() {
    return {
      // 总条数
      total: 20,
      // 页码
      page: 1,
      // 页容量
      limit: 20,
      id: "",
      src: "",
      mvs: [],
      details: {},
      hotComments: [],
      comments: [],
    };
  },
  methods: {
    toMV(id) {
      this.page = 1;
      this.$router.push("/mv?id=" + id);
      this.list();
      this.update();
      this.detail();
      this.comment();
    },
    handleCurrentChange(val) {
      this.page = val;
      axios({
        url: "https://autumnfish.cn/comment/mv",
        method: "get",
        params: {
          id: this.id,
          limit: this.limit,
          offset: (this.page - 1) * this.limit,
        },
      }).then((res) => {
        this.comments = res.data.comments;
        this.total = res.data.total;
        for (let i = 0; i < this.comments.length; i++) {
          var time = new Date(this.comments[i].time);
          var y = time.getFullYear();
          var m = time.getMonth() + 1;
          m = m < 10 ? "0" + m : m;
          var d = time.getDate();
          d = d < 10 ? "0" + d : d;
          var h = time.getHours();
          h = h < 10 ? "0" + h : h;
          var mm = time.getMinutes();
          mm = mm < 10 ? "0" + mm : mm;
          var s = time.getSeconds();
          s = s < 10 ? "0" + s : s;
          this.comments[i].time =
            y + "-" + m + "-" + d + " " + h + ":" + mm + ":" + s;
        }
      });
    },
    list() {
      this.id = this.$route.query.id;
      axios({
        url: "https://autumnfish.cn/mv/url",
        method: "get",
        params: {
          id: this.id,
        },
      }).then((res) => {
        console.log(res);
        this.src = res.data.data.url;
      });
    },
    update() {
      axios({
        url: "https://autumnfish.cn/simi/mv",
        method: "get",
        params: {
          mvid: this.id,
        },
      }).then((res) => {
        console.log(res);
        this.mvs = res.data.mvs;
      });
    },
    detail() {
      axios({
        url: "https://autumnfish.cn/mv/detail",
        method: "get",
        params: {
          mvid: this.id,
        },
      }).then((res) => {
        console.log(res);
        this.details = res.data.data;
      });
    },
    comment() {
      axios({
        url: "https://autumnfish.cn/comment/mv",
        method: "get",
        params: {
          id: this.id,
          limit: this.limit,
          offset: (this.page - 1) * this.limit,
        },
      }).then((res) => {
        console.log(res);
        this.hotComments = res.data.hotComments;
        this.comments = res.data.comments;
        this.total = res.data.total;
        for (let i = 0; i < this.comments.length; i++) {
          var time = new Date(this.comments[i].time);
          var y = time.getFullYear();
          var m = time.getMonth() + 1;
          m = m < 10 ? "0" + m : m;
          var d = time.getDate();
          d = d < 10 ? "0" + d : d;
          var h = time.getHours();
          h = h < 10 ? "0" + h : h;
          var mm = time.getMinutes();
          mm = mm < 10 ? "0" + mm : mm;
          var s = time.getSeconds();
          s = s < 10 ? "0" + s : s;
          this.comments[i].time =
            y + "-" + m + "-" + d + " " + h + ":" + mm + ":" + s;
        }
        for (let i = 0; i < this.hotComments.length; i++) {
          var time = new Date(this.hotComments[i].time);
          var y = time.getFullYear();
          var m = time.getMonth() + 1;
          m = m < 10 ? "0" + m : m;
          var d = time.getDate();
          d = d < 10 ? "0" + d : d;
          var h = time.getHours();
          h = h < 10 ? "0" + h : h;
          var mm = time.getMinutes();
          mm = mm < 10 ? "0" + mm : mm;
          var s = time.getSeconds();
          s = s < 10 ? "0" + s : s;
          this.hotComments[i].time =
            y + "-" + m + "-" + d + " " + h + ":" + mm + ":" + s;
        }
      });
    },
  },
};
</script>

<style></style>

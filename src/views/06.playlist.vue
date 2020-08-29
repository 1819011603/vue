<template>
  <div class="playlist-container">
    <div class="top-wrap">
      <div class="img-wrap">
        <img :src="playlist.coverImgUrl" alt="" />
      </div>
      <div class="info-wrap">
        <p class="title">{{playlist.name}}</p>
        <div class="author-wrap">
          <img class="avatar" :src="playlist.creator?playlist.creator.avatarUrl:''" alt="" />
          <span class="name">{{playlist.creator?playlist.creator.nickname:''}}</span>
          <span class="time">{{playlist.createTime}} 创建</span>
        </div>
        <div class="play-wrap">
          <span class="iconfont icon-circle-play"></span>
          <span class="text">播放全部</span>
        </div>
        <div class="tag-wrap">
          <span class="title">标签:</span>
          <ul>
            <li v-for="(item,index) in playlist.tags" :key="index">{{item}}</li>
          </ul>
        </div>
        <div class="desc-wrap">
          <span class="title">简介:</span>
          <span class="desc">{{playlist.description}}</span
          >
        </div>
      </div>
    </div>
    <el-tabs v-model="activeIndex">
      <el-tab-pane label="歌曲列表" name="1">
        <table class="el-table playlit-table">
          <thead>
            <th></th>
            <th></th>
            <th>音乐标题</th>
            <th>歌手</th>
            <th>专辑</th>
            <th>时长</th>
          </thead>
          <tbody>
            <tr class="el-table__row" v-for="(item,index) in tracks" :key="index">
              <td>{{index+1}}</td>
              <td>
                <div class="img-wrap">
                  <img :src="item.al.picUrl" alt="" />
                  <span class="iconfont icon-play" @click="play(item.id)"></span>
                </div>
              </td>
              <td>
                <div class="song-wrap">
                  <div class="name-wrap">
                    <span>{{item.name}}</span>
                    <span class="iconfont icon-mv"></span>
                  </div>
                  <span>{{item.al.name}}</span>
                </div>
              </td>
              <td>{{item.ar[0].name}}</td>
              <td>{{item.al.name}}</td>
              <td>06:03</td>
            </tr>
          </tbody>
        </table>
      </el-tab-pane>
      <el-tab-pane label="评论" name="2">
        <!-- 精彩评论 -->
        <div class="comment-wrap">
          <p class="title">精彩评论<span class="number">({{commentsNum}})</span></p>
          <div class="comments-wrap">
            <div class="item" v-for="(item,index) in hotComments" :key="index">
              <div class="icon-wrap">
                <img :src="item.user.avatarUrl" alt="" />
              </div>
              <div class="content-wrap">
                <div class="content">
                  <span class="name">{{item.user.nickname}}：</span>
                  <span class="comment">{{item.content}}</span>
                </div>
                <div class="re-content" v-if="item.beReplied.length!=0">
                  <span class="name">{{item.beReplied[0].user.nickname}}：</span>
                  <span class="comment">{{item.beReplied[0].content}}</span>
                </div>
                <div class="date">2020-02-12 17:26:11</div>
              </div>
            </div>
          </div>
        </div>
        <!-- 最新评论 -->
        <div class="comment-wrap">
          <p class="title">最新评论<span class="number">({{total}})</span></p>
          <div class="comments-wrap">
            <div class="item" v-for="(item,index) in comments" :key="index">
              <div class="icon-wrap">
                <img :src="item.user.avatarUrl" alt="" />
              </div>
              <div class="content-wrap">
                <div class="content">
                  <span class="name">{{item.user.nickname}}：</span>
                  <span class="comment">{{item.content}}</span>
                </div>
                <div class="re-content" v-if="item.beReplied.length!=0">
                  <span class="name">{{item.beReplied[0].user.nickname}}：</span>
                  <span class="comment">{{item.beReplied[0].content}}</span>
                </div>
                <div class="date">2020-02-12 17:26:11</div>
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
          :page-size="20"
        >
        </el-pagination>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'playlist',
  created(){
      let id = this.$route.query.id
      axios.get('https://autumnfish.cn/playlist/detail?id=' + id).then(res=>{
        console.log(res)
        this.playlist = res.data.playlist
        this.tracks = this.playlist.tracks
    }).catch(err=>console.log(err))
    axios({
      url:'https://autumnfish.cn/comment/hot',
      method:'get',
      params:{
        id,
        type:2
      }

    }).then(res=>{
      console.log(res)
      this.hotComments = res.data.hotComments
      console.log(this.hotComments)
      this.commentsNum = res.data.total
      console.log(this.commentsNum)
    })
    axios({
      url:'https://autumnfish.cn/comment/playlist',
      method:'get',
      params:{
        id,
        'limit':20,
        'offset':(this.page-1)*20
      }

    }).then(res=>{
      console.log(res)
      this.total = res.data.total
      this.comments = res.data.comments
    })
    
  },
  data() {
    return {
      activeIndex: '1',
      // 总条数
      total: 0,
      // 页码
      page: 1,
      playlist:{},
      tracks:[],
      hotComments:[],
      commentsNum:0,
      comments:[],
    };
  },
  methods: {
    handleCurrentChange(val) {
      this.page = val
      let id = this.$route.query.id
      axios({
      url:'https://autumnfish.cn/comment/playlist',
      method:'get',
      params:{
        id,
        'limit':20,
        'offset':(this.page-1)*20
      }

    }).then(res=>{
      console.log(res)
      this.total = res.data.total
      this.comments = res.data.comments
    })
      
    },
    play(id){
      axios.get("https://autumnfish.cn/song/url?id="+id).then(res=>{
        this.$parent.musicUrl = res.data.data[0].url
      })

    }
  }
};
</script>

<style >

</style>

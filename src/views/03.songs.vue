<template>
  <div class="songs-container">
    <div class="tab-bar">
      <span class="item" :class="{active:id == 0}" @click="id=0">全部</span>
      <span class="item" :class="{active:id == 7}" @click="id=7">华语</span>
      <span class="item" :class="{active:id == 96}" @click="id=96">欧美</span>
      <span class="item" :class="{active:id == 8}" @click="id=8">日本</span>
      <span class="item" :class="{active:id == 16}" @click="id=16">韩国</span>
    </div>
    <!-- 底部的table -->
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
        <tr class="el-table__row" v-for="(item,index) in music" :key="index">
          <td>{{index+1}}</td>
          <td>
            <div class="img-wrap">
              <img :src="item.album.picUrl" alt="" />
              <span @click="play(item.id)" class="iconfont icon-play"></span>
            </div>
          </td>
          <td>
            <div class="song-wrap">
              <div class="name-wrap">
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
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'songs',
  created(){
    this.Song()
  },
  data() {
    return {
      music:[],
      id:0,
    };
  },
  watch:{
    id(){
      // console.log(this.id)
      this.Song()
    }
  },
  methods:{
    Song(){
      axios.get("https://autumnfish.cn/top/song?type="+this.id).then(res=>{
      this.music = res.data.data
      let duration
      for(let i = 0;i < this.music.length;i++){
        duration=parseInt(this.music[i].duration/1000)
        let m = parseInt(duration/60);
        if(m<10)m='0'+m
        let s = duration%60
        if(s<10)s='0'+s
        this.music[i].duration=`${m}:${s}`
      }
    }).catch(err=>console.log(err))
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

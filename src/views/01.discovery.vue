<template>
  <div class="discovery-container">
    <!-- 轮播图 -->
    <el-carousel class="" :interval="4000" type="card">
      <el-carousel-item v-for="(item,index) in lbtImgs" :key="index">
        <img :src="item.imageUrl" alt="" />
      </el-carousel-item>
    </el-carousel>
    <!-- 推荐歌单 -->
    <div class="recommend">
      <h3 class="title">
        推荐歌单
      </h3>
      <div class="items">
        <div v-for="(item,index) in result" class="item" :key="index" @click="enterList(item.id)">
          <div class="img-wrap">
            <div class="desc-wrap">
              <span class="desc">{{item.copywriter}}</span>
            </div>
            <img :src="item.picUrl" alt="" />
            <span class="iconfont icon-play"></span>
          </div>
          <p class="name">{{item.name}}</p>
        </div>
        
      </div>
    </div>
    <!-- 最新音乐 -->
    <div class="news">
      <h3 class="title">
        最新音乐
      </h3>
      <div class="items">
        <div v-for="(item,index) in newSongs" class="item" :key="index">
          <div class="img-wrap">
            <img :src="item.picUrl" alt="" />
            <span class="iconfont icon-play" @click="playMusic(item.id)"></span>
          </div>
          <div class="song-wrap">
            <div class="song-name">{{item.name}}</div>
            <div class="singer">{{item.song.artists[0].name}}</div>
          </div>
        </div>
        
      </div>
    </div>
    <!-- 推荐MV -->
    <div class="mvs">
      <h3 class="title">推荐MV</h3>
      <div class="items">
        <div v-for="(item,index) in mvs" :key="index" class="item" @click="toMV(item.id)">
          <div class="img-wrap">
            <img :src="item.picUrl" alt="" />
            <span class="iconfont icon-play"></span>
            <div class="num-wrap">
              <div class="iconfont icon-play"></div>
              <div class="num">{{item.playCount}}</div>
            </div>
          </div>
          <div class="info-wrap">
            <div class="name">{{item.copywriter}}</div>
            <div class="singer">{{item.artists[0].name}}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'discovery',
  data(){
    return {
      lbtImgs:[],
      result:[],
      newSongs:[],
      isplay:false,
      mvs:[],
    }
  },
  created(){
    axios.get('https://autumnfish.cn/banner').then(res=>{
        this.lbtImgs = res.data.banners
        
    }).catch(err=>console.log(err))
    axios.get('https://autumnfish.cn/personalized?limit=10').then(res=>{
        this.result=res.data.result
    }).catch(err=>console.log(err))
    axios.get('https://autumnfish.cn/personalized/newsong').then(res=>{
      
        this.newSongs=res.data.result
    }).catch(err=>console.log(err))
    
    axios.get('https://autumnfish.cn/personalized/mv').then(res=>{
      
        this.mvs=res.data.result
    }).catch(err=>console.log(err))
    
  },
  methods:{
    playMusic(id){
      axios.get('https://autumnfish.cn/song/url?id='+id).then(res=>{
        this.$parent.musicUrl=res.data.data[0].url
        
    }).catch(err=>console.log(err))
    },
    enterList(id){
      this.$router.push("/playlist?id="+id)
    },
    toMV(id){
      this.$router.push('/mv?id='+id)
    }

  }
};
</script>

<style >

</style>

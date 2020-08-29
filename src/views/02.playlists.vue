<template>
  <div class="playlists-container">
    <!-- 同步 -->
    <div class="top-card">
      <div class="icon-wrap">
        <img :src="playlists.coverImgUrl" alt="" />
      </div>
      <div class="content-wrap" >
        <div class="tag">精品歌单</div>
        <div class="title">
          {{playlists.name}}
        </div>
        <div class="info">
          {{playlists.description}}
        </div>
      </div>
      <img src="../assets/listCover.jpg" alt="" class="bg" />
      <div class="bg-mask"></div>
    </div>
    <div class="tab-container">
      <!-- tab栏 顶部 -->
      <div class="tab-bar">
        <span class="item" :class="{active:cat=='全部'}" @click="cat='全部'">全部</span>
        <span class="item" :class="{active:cat=='欧美'}" @click="cat='欧美'">欧美</span>
        <span class="item" :class="{active:cat=='华语'}" @click="cat='华语'">华语</span>
        <span class="item" :class="{active:cat=='流行'}" @click="cat='流行'">流行</span>
        <span class="item" :class="{active:cat=='说唱'}" @click="cat='说唱'">说唱</span>
        <span class="item" :class="{active:cat=='摇滚'}" @click="cat='摇滚'">摇滚</span>
        <span class="item" :class="{active:cat=='民谣'}" @click="cat='民谣'">民谣</span>
        <span class="item" :class="{active:cat=='电子'}" @click="cat='电子'">电子</span>
        <span class="item" :class="{active:cat=='轻音乐'}" @click="cat='轻音乐'">轻音乐</span>
        <span class="item" :class="{active:cat=='影视原声'}" @click="cat='影视原声'">影视原声</span>
        <span class="item" :class="{active:cat=='ACG'}" @click="cat='ACG'">ACG</span>
        <span class="item" :class="{active:cat=='怀旧'}" @click="cat='怀旧'">怀旧</span>
        <span class="item" :class="{active:cat=='治愈'}" @click="cat='治愈'">治愈</span>
        <span class="item" :class="{active:cat=='旅行'}" @click="cat='旅行'">旅行</span>
      </div>
      <!-- tab的内容区域 -->
      <div class="tab-content">
        <div class="items">
          <div v-for="(item,index) in playlist" :key="index" class="item" @click="enterList(item.id)">
            <div class="img-wrap">
              <div class="num-wrap">
                播放量:
                <span class="num">{{item.playCount >= 100000?Math.round(item.playCount/10000)+'万':item.playCount}}</span>
              </div>
              <img :src="item.coverImgUrl" alt="" />
              <span class="iconfont icon-play"></span>
            </div>
            <p class="name">{{item.description}}</p>
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
      :page-size="10"
    >
    </el-pagination>
    
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'recommend',
  created(){
    this.top()
    this.list()
  },
  watch:{
    cat(){
      this.page=1
      this.list()
      this.top()
    }
  },
  data() {
    return {
      // 总条数
      total:1,
      // 页码
      page:1 ,
      cat:"全部",
      playlists:{},
      playlist:[],
      id:'',
    };
  },
  methods: {
    enterList(id){
      this.$router.push('/playlist?id='+id)
      
    }
    ,
    top(){
      axios.get('https://autumnfish.cn/top/playlist/highquality?limit=1&cat='+this.cat).then(res=>{
        this.playlists=res.data.playlists[0]
    }).catch(err=>console.log(err))

    },
    list(){
      axios({
      url:'https://autumnfish.cn/top/playlist/',
      method:'get',
      params:{
        limit:10,
        offset:(this.page-1)*10,
        cat:this.cat
      }

    }).then(res=>{
      this.total=res.data.total
      this.playlist=res.data.playlists
    })
    },
    handleCurrentChange(val) {
      this.page=val
      this.list()
    }
  }
};
</script>

<style >

</style>

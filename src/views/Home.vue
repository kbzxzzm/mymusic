<template>
  <div class="home">
    <input type="text" @keyup.enter="searchmusic" v-model="query" placeholder="点这儿开启音乐之门🎵" />
    <h6>当前封面</h6>
    <img :src="musicimage" alt />
    <h6>播放列表</h6>
    <!-- 搜索歌曲列表 -->
    <div class="song_wrapper">
      <ul class="song_list">
        <li v-for="(item,index) in musiclist" :key="index">
          <a href="javascript:;" @click="playMusic(item.id)">播放</a>
          <b>{{item.name}}</b>
          <span>
            <a href="javascript:;" v-if="item.mvid!=0" @click="playMV(item.mvid)">mv</a>
          </span>
        </li>
      </ul>
    </div>播放器
    <audio ref="audio" :src="musicUrl" controls autoplay loop class="myaudio"></audio>
    <hr />
    <h2>mv</h2>
    <video :src="mvurl" controls autoplay loop></video>

    <div id="pinglun">
      <h6>评论列表</h6>
      <p v-for="(item,id) in pinglun" :key="id">{{item.content}}</p>
      <br>
    </div>
  </div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      query: "",
      musiclist: [],
      musicUrl: "",
      musicimage: "",
      pinglun: [],
      mvurl: ""
    };
  },
  methods: {
    async searchmusic() {
      const res = await this.$axios.get(
        "https://autumnfish.cn/search?keywords=" + this.query //🔍搜索歌曲
      );
      this.musiclist = res.data.result.songs;
    },
    async playMusic(musicId) {
      const res = await this.$axios.get(
        "https://autumnfish.cn/song/url?id=" + musicId
      );
      this.musicUrl = res.data.data[0].url;
      const image = await this.$axios.get(
        "https://autumnfish.cn/song/detail?ids=" + musicId
      );
      this.musicimage = image.data.songs[0].al.picUrl;
      const pl = await this.$axios.get(
        "https://autumnfish.cn/comment/hot?type=0&id=" + musicId
      );
      this.pinglun = pl.data.hotComments;
    },
    async playMV(mvid) {
      const res = await this.$axios.get(
        "https://autumnfish.cn/mv/url?id=" + mvid
      );
      this.mvurl = res.data.data.url;
    }
  },
  created() {}
};
</script>

<style>
ul li {
  list-style: none;
}
img {
  width: 90px;
  height: 90px;
}
* {
  margin: 0;
  padding: 0;
}
video {
  width: 500px;
  height: 520px;
  position: absolute;
  right: 0;
  top: 0;
}
#pinglun {
  position: absolute;
  top: 100px;
  width: 400px;
  left: 420px;
}
#pinglun p{
  margin-top: 40px;
}
</style>
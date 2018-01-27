<template>
  <transition name="slide">
    <music-list :title="title" :bg-image="bgImage" :songs="songs"></music-list>
  </transition>

</template>

<script type="text/ecmascript-6">
  import {mapGetters} from 'vuex'
  import {getSingerDetail} from 'api/singer'
  import {ERR_OK} from 'api/config'
  import {createSong} from 'common/js/song'
  import MusicList from 'components/music-list/music-list'
  export default {
    components:{MusicList},
    data(){
      return {
        songs:[]
      }
    },
    computed:{
      title() {
        return this.singer.name
      },
      bgImage() {
        return this.singer.avatar
      },
      ...mapGetters([
        'singer'
      ])
    },
    created(){
      this._getDetail()
      console.log(this.singer);
    },
    methods:{
      _normalizeSongs(list) {
        let ret = []
        list.forEach((item) => {
          let {musicData} = item // 其实musicData=item.musicData
          if (musicData.songid && musicData.albummid) {
            ret.push(createSong(musicData))
          }
        })
        return ret
      },
      _getDetail(){
        if (!this.singer.id) { //如果这个id不存在，或者id不存在，回到歌手的那个路由
          this.$router.push('/singer')
          return
        }
        getSingerDetail(this.singer.id).then((res) => {
          if(res.code===ERR_OK){
            this.songs = this._normalizeSongs(res.data.list)
            console.log(this.songs);
          }
        })
      }
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .slide-enter-active, .slide-leave-active
    transition:all 0.3s

  .slide-enter, .slide-leave-to
    transform: translate3d(100%, 0, 0)

</style>

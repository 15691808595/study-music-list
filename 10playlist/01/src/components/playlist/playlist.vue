<template>
  <transition name="list-fade">
    <div class="playlist" v-show="showFlag" @click="hide">
      <div class="list-wrapper" @click.stop>
        <div class="list-header">
          <h1 class="title">
            <i class="icon"></i>
            <span class="text"></span>
            <span class="clear" @click="showConfirm"><i class="icon-clear"></i></span>
          </h1>
        </div>
        <scroll :data="sequenceList" ref="listContent" class="list-content">
          <ul ref="list" >
            <li class="item" v-for="(item,index) in  sequenceList" ref="listItem"
                @click="selectItem(item,index)"
            >
              <i class="current" :class="getCurrentIcon(item)"></i>
              <span class="text">{{item.name}}</span>
              <span class="like">
                <i></i>
              </span>
              <span class="delete">
                <i class="icon-delete"></i>
              </span>
            </li>
          </ul>
        </scroll>
        <div class="list-operate" @click="addSong">
          <div class="add">
            <i class="icon-add"></i>
            <span class="text">添加歌曲到队列</span>
          </div>
        </div>
        <div @click="hide" class="list-close">
          <span>关闭</span>
        </div>
      </div>
      <!--<confirm ref="confirm" @confirm="confirmClear" text="是否清空播放列表" confirmBtnText="清空"></confirm>-->
      <!--<add-song></add-song>-->
    </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  import {mapActions,mapGetters,mapMutations} from 'vuex'
  import {playMode} from 'common/js/config'
  import Scroll from 'base/scroll/scroll'
  import Confirm from 'base/confirm/confirm'
  import {playerMixin} from 'common/js/mixin'
  import AddSong from 'components/add-song/add-song'

  export default {
    mixins: [playerMixin],
    components: {
      Scroll,
      Confirm,
      AddSong
    },
    data() {
      return {
        showFlag: false,
        refreshDelay: 120
      }
    },
    computed: {
      ...mapGetters([
        'sequenceList',
        'currentSong',
        'palylist',
        'mode'
      ]),
      modeText() {

      }
    },
    methods: {
      show() {
        this.showFlag = true;
        //show or hide也要让重新计算，所有重新调用refresh()
        setTimeout(()=>{
          this.$refs.listContent.refresh();
          this.scrollToCurrent(this.currentSong)

        },20)
      },
      hide() {
        this.showFlag = false;

      },
      showConfirm() {

      },
      confirmClear() {

      },
      getCurrentIcon(item) {
        if (this.currentSong.id === item.id) {
          return 'icon-play'
        }
        return ''
      },
      selectItem(item, index) {
        if (this.mode === playMode.random) {
          index = this.playlist.findIndex((song) => {
            return song.id === item.id
          })
        }
        this.setPlayingState(true)
        this.setCurrentIndex(index)
      },
      ...mapMutations({
        'setCurrentIndex':'SET_CURRENT_INDEX',
        'setPlayingState':'SET_PLAYING_STATE'
      }),
      scrollToCurrent(current) {
        const index=this.sequenceList.findIndex((song)=>{
          return current.id===song.id
        });
        this.$refs.listContent.scrollToElement(this.$refs.listItem[index],300)
      },
      deleteOne(item) {

      },
      addSong() {
        this.$refs.addSong.show()
      },
      ...mapActions([])
    },
    watch: {
      currentSong(newSong,oldSong){
        if(!this.showFlag || newSong.id === oldSong.id){
          return
        }
        this.scrollToCurrent(newSong)
      }
    },
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"
  @import "~common/stylus/mixin"

  .playlist
    position: fixed
    left: 0
    right: 0
    top: 0
    bottom: 0
    z-index: 200
    background-color: $color-background-d
    &.list-fade-enter-active, &.list-fade-leave-active
      transition: opacity 0.3s
      .list-wrapper
        transition: all 0.3s
    &.list-fade-enter, &.list-fade-leave-to
      opacity: 0
      .list-wrapper
        transform: translate3d(0, 100%, 0)
    &.list-fade-enter
    .list-wrapper
      position: absolute
      left: 0
      bottom: 0
      width: 100%
      background-color: $color-highlight-background
      .list-header
        position: relative
        padding: 20px 30px 10px 20px
        .title
          display: flex
          align-items: center
          .icon
            margin-right: 10px
            font-size: 30px
            color: $color-theme-d
          .text
            flex: 1
            font-size: $font-size-medium
            color: $color-text-l
          .clear
            extend-click()
            .icon-clear
              font-size: $font-size-medium
              color: $color-text-d
      .list-content
        max-height: 240px
        overflow: hidden
        .item
          display: flex
          align-items: center
          height: 40px
          padding: 0 30px 0 20px
          overflow: hidden
          &.list-enter-active, &.list-leave-active
            transition: all 0.1s
          &.list-enter, &.list-leave-to
            height: 0
          .current
            flex: 0 0 20px
            width: 20px
            font-size: $font-size-small
            color: $color-theme-d
          .text
            flex: 1
            no-wrap()
            font-size: $font-size-medium
            color: $color-text-d
          .like
            extend-click()
            margin-right: 15px
            font-size: $font-size-small
            color: $color-theme
            .icon-favorite
              color: $color-sub-theme
          .delete
            extend-click()
            font-size: $font-size-small
            color: $color-theme
      .list-operate
        width: 140px
        margin: 20px auto 30px auto
        .add
          display: flex
          align-items: center
          padding: 8px 16px
          border: 1px solid $color-text-l
          border-radius: 100px
          color: $color-text-l
          .icon-add
            margin-right: 5px
            font-size: $font-size-small-s
          .text
            font-size: $font-size-small
      .list-close
        text-align: center
        line-height: 50px
        background: $color-background
        font-size: $font-size-medium-x
        color: $color-text-l
</style>

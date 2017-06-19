<template>
  <div id="app">
    <div class="g-sd">
      <div class="m-list">
          <div class="list-name" @click="slideList()">
              河南电台
              <i class="icon-down"></i>
          </div>
          <ul class="list-box" v-show="showBtn">
              <li class="list-item" v-for="n in 10">人民广播</li>
          </ul>
      </div>
      <div class="m-list">
          <div class="list-name">
              网络电台
              <i class="icon-down"></i>
          </div>
          <ul class="list-box">
              <li class="list-item">人民广播</li>
          </ul>
      </div>
      <div class="m-list">
          <div class="list-name">
              地市台
              <i class="icon-down"></i>
          </div>
          <ul class="list-box">
              <li class="list-item">人民广播</li>
          </ul>
      </div>
    </div>
    <div class="g-mn">
      <div class="g-mnc">
        <p>左侧顶宽</p>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'app',
  data() {
    return {
        showBtn:false
    }
  },
  methods:{
    slideList() {
        this.showBtn = !this.showBtn
    }
  }
}
</script>

<style scoped lang="stylus">
#app
    position: absolute
    top 0
    bottom 0
    .g-sd
        position: relative
        float left
        width 160px
        height 100%
        margin-right -160px
        overflow auto
        background #333
        .m-list
            .list-name
                position: relative
                width 100%
                height 40px
                line-height 40px
                text-align center
                font-size 15px
                color #222
                background #f8f8f8
                margin-top 1px
                cursor: pointer
                .icon-down
                    position: absolute
                    line-height 40px
                    right 24px
                    font-size 10px
            .list-box
                .list-item
                    width 100%
                    height 40px
                    line-height 40px
                    text-align center
                    font-size 15px
                    color #fff
                    cursor: pointer
                    &:hover
                        background blue
    .g-mn
        float right 
        width 100%
        height 100%
        .g-mnc
            margin-left 160px
            min-width 970px
            height 100%
            background #eee
</style>

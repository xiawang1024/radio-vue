<template>
  <div id="app">
    <div class="g-logo">
        <a href="http://www.hndt.com/"><img src="./imgs/logo.png"></a>
    </div>
    <div class="g-bg-color">
        <div class="bg-item" v-for="item of bgColors" :style="{backgroundColor:item.color}" title="设置背景色" @click="setBgColor(item.color)"></div>
    </div>
    <div class="g-sd">
      <div class="m-list">
          <div class="list-name" @click="slideList(0)">
              河南电台
              <i :class="showBtn[0] ? 'icon-down' : 'icon-right'"></i>
          </div>
          <ul class="list-box" v-show="showBtn[0]">
              <li class="list-item" v-for="item of hnItemList" @click="isActive(item.cid)" :class="{isActive: cid == item.cid ? true : false}">{{item.name}}</li>
          </ul>
      </div>
      <div class="m-list">
          <div class="list-name" @click="slideList(1)">
              网络电台
              <i :class="showBtn[1] ? 'icon-down' : 'icon-right'"></i>
          </div>
          <ul class="list-box" v-show="showBtn[1]">
              <li class="list-item" v-for="item of wlItemList" @click="isActive(item.cid)" :class="{isActive: cid == item.cid ? true : false}">{{item.name}}</li>
          </ul>
      </div>
      <div class="m-list">
          <div class="list-name" @click="slideList(2)">
              地市台
              <i :class="showBtn[2] ? 'icon-down' : 'icon-right'"></i>
          </div>
          <ul class="list-box" v-show="showBtn[2]">
              <li class="list-item" v-for="item of dsItemList" @click="isActive(item.cid)" :class="{isActive: cid == item.cid ? true : false}">{{item.name}}</li>
          </ul>
      </div>
    </div>
    <div class="g-mn">
      <div class="g-mnc clearfix">
        <div class="m-daylist" >
            <div class="m-datepick clearfix" v-show="itemList.length > 0">
                <div class="item"  @mouseenter="yearBtn = true" @mouseleave="yearBtn = false">
                    <input class="ipt" type="text" readonly name="" value="2017年" v-model="year" ><span class="select"><i class="white icon-down"></i></span>
                    <ul class="yearBox" v-show="yearBtn">
                        <li v-for="item of years" @click="selectYear(item.id)">{{item.id}}年</li>
                    </ul>
                </div>
                <div class="item" @mouseenter="monthBtn = true" @mouseleave="monthBtn = false">
                    <input class="ipt" type="text" readonly name="" value="06月" v-model="month"><span class="select" ><i class="white icon-down"></i></span>
                    <ul class="monthBox" v-show="monthBtn">
                        <li v-for="n in 12" @click="selectMonth(n)">{{n < 10 ? '0' + n : n}}月</li>
                    </ul>
                </div>
                <div class="item" @mouseenter="dayBtn = true" @mouseleave="dayBtn = false">
                    <input class="ipt" type="text" readonly name="" value="02日" v-model="day"><span class="select" ><i class="white icon-down"></i></span>
                    <ul class="dayBox" v-show="dayBtn">
                        <li v-for="day in days" @click="selectDay(day)">{{day}}日</li>
                    </ul>
                </div>
            </div>
            <div class="m-itemlist" v-show="itemList.length > 0">
                <div class="item-hd"></div>
                <div class="item-ft"></div>
                <!-- <div class="item-middle"></div> -->
                <div class="listwrap">
                    <ul @mousewheel='scrollTo($event)'>
                        <li class="list-item" :class="{listIsActive : activeItemIndex == index ? true : false}" v-for="(item,index) of itemList" @click="selectItem(index,item.playUrl,item.title,item.beginTime,item.endTime)">
                            <span class="list-time">
                                {{item.beginTime |formdata}} - {{item.endTime |formdata}}
                            </span>
                            <span class="list-title">{{item.title}}</span>
                            <span class="play-icon" v-show="item.playUrl && item.playUrl.length > 0"></span><!-- v-show="item.playUrl && item.playUrl.length > 0" -->
                        </li>
                    </ul>
                </div>
            </div>
            <div class="liveItemInfo">
                <div class="m-txt">
                    <span class="lising">正在收听</span>
                    <span class="m-time">
                        {{dateSrc | formdate}}  {{timeSrc}}    
                    </span>
                </div>                
                <span class="m-item" v-html="nameSrc"></span>
            </div>
        </div>
        <div class="m-disc">
            <div class="disc-bg"></div>
            <div class="disc-wrap">
                <img :src="imgSrc" alt="" class="disc-img">
            </div>
            <div class="disc-arm"></div>
            <div class="playBtn">
                
            </div>
        </div>
      </div>
    </div>
    <div class="g-play">
        <!-- <span class="m-voice"></span> -->
        <!-- <vue-slider v-model="options.value" v-bind="options"></vue-slider> -->
        <wvoice></wvoice>
        <!-- <span class="m-time">
            {{dateSrc | formdate}}  {{timeSrc}}    
        </span>
        <span class="m-item" v-html="nameSrc">
        </span> -->
        <div class="volume" @mouseenter="isShowSlide = true" @mouseleave="isShowSlide = false">
            <vue-slider v-if="isShowSlide"  class="vo-slide" v-model="options.value" v-bind="options"></vue-slider>
        </div>
    </div>
    <div class="audio">
       <!--  <video id=example-video width=600 height=300 class="video-js vjs-default-skin" controls>
          <source
             src="http://stream.hndt.com:1935/live/xinwen/playlist.m3u8"
             type="application/x-mpegURL">
        </video> -->
    </div>
    <span style="display: none">{{stamp}}</span>
  </div>
</template>

<script>
import { getLiveItem, getClassItem, getChannelItem, clickItem } from '@/api.js'
import voice from './voice.vue'
import vueSlider from 'vue-slider-component';
const bgColors = [
    {color:"#f8f8f8"},
    {color:"#fac523"},
    {color:"#99c45e"},
    {color:"#78c2c4"},
    {color:"#b473a2"},
]
const years = [
    {id:2017},
    {id:2016},
]
export default {
  name: 'app',
  components:{
    wvoice:voice,
    vueSlider
  },  
  data() {
    return {
        bgColors:bgColors,
        showBtn:[true,false,false],
        yearBtn:false,
        monthBtn:false,
        dayBtn:false,
        top:-120,
        years:years,
        year:new Date().getFullYear() + '年',
        month:new Date().getMonth() + 1 + '月',
        day:new Date().getDate() + '日',
        audioSrc:'',
        imgSrc:"",
        timeSrc:'',
        nameSrc:'',
        dateSrc:'',
        hnItemList:[], //河南电台列表
        wlItemList:[], //网络电台列表
        dsItemList:[], //地市台列表
        itemList:[], //节目列表
        cid:1, //河南电台新闻广播cid
        player:null,
        activeItemIndex:0,//点击节目索引
        isShowSlide:false,//是否显示音量组件
        options:{
            value: 80,// 值
            width: 4,// 组件宽度
            height: 150,// 组件高度
            direction: "vertical",// 组件方向
            dotSize: 18,// 滑块大小
            eventType: "auto",// 事件类型
            min: 0,// 最小值
            max: 100,// 最大值
            interval: 1,// 分段间隔
            disabled: false,// 是否不可用
            show: true,// 是否显示组件
            realTime: false,// 是否实时计算组件布局
            tooltip: "always",// 是否显示工具提示
            clickable: true,// 是否可点击的
            tooltipDir: "top",// 工具提示方向
            piecewise: false,// 是否显示分段样式
            lazy: false,// 是否在拖拽结束后同步值
            reverse: false,// 是否反向组件
            speed: 0.5,// 动画速度
            formatter: null,// 格式化tooltip的值
            bgStyle: {
                backgroundColor:'#ccc'
            },// 组件背景样式
            sliderStyle: {
                backgroundColor:'#1ba2ff'
            },// 滑块样式
            tooltipStyle: {
                backgroundColor:'#1ba2ff'
            },// 工具提示样式
            processStyle: {
                backgroundColor:'#1ba2ff'
            },// 进度条样式
            piecewiseStyle: null,// 分割点的样式
        }
    }
  },
  created(){
    getClassItem(1).then((res) => {
        let data = res.data;
        this.hnItemList = data;
        this.$nextTick(function(){})
    })
    getClassItem(2).then((res) => {
        let data = res.data;
        this.wlItemList = data;
        this.$nextTick(function(){})
    })
    getClassItem(3).then((res) => {
        let data = res.data;
        this.dsItemList = data;
        this.$nextTick(function(){})
    })
    getChannelItem(1).then((res) => {
        let data = res.data;
        this.itemList = data.programs;
        this.imgSrc = 'http://program.hndt.com' + data.image;
        this.timeSrc = data.time;
        this.nameSrc = data.live;
        this.audioSrc = data.streams[0];
        showPlayer(this.audioSrc)
        this.$nextTick(() => {
            this.isPlayIndex()
            $('.listwrap').scrollTop(this.top - 120);
        })
    })
    this.dateSrc =(new Date()).getTime();
  },
  mounted(){
    // var player = videojs('example-video');
    // player.play();
    
  },
  watch:{
    year(){
        $('.listwrap').scrollTop(0);
        this.top = 0 ;
    },
    month(){
        $('.listwrap').scrollTop(0);
        this.top = 0 ;
    },
    day(){
        $('.listwrap').scrollTop(0);
        this.top = 0 ;
    },
    options:{
        handler:function(){
            CKobject.getObjectById('ck-video').changeVolume(this.options.value)
        },
        deep: true
    }
  },
  computed:{
    days() {
        var year = parseInt(this.year);
        var daylen=0;
        if (/1|3|5|7|8|10|12/.test(this.month)) {
            daylen = 31;
        }
        else if (/4|6|9|11/.test(this.month)) {
            daylen=30;
        }
        else if (/2/.test(this.month)) {
            if (year % 4 === 0 && year % 100 !==0 || year % 400 === 0) {
                daylen=29;
            }
            else {
                daylen=28;
            }
        }
        return daylen
    },
    stamp() {
        //2015-03-05 17:59:00.0
        var year,month= parseInt(this.month),day = parseInt(this.day);
        if(month < 10){
            month = '0' + month
        }
        if(day < 10){
            day = '0' + day;
        }
        var date = parseInt(this.year) + '-' + month + '-' + day;
        var date = date + ' 00:00:00.0';
        var datestamp = this.timeToStamp(date);
        clickItem(this.cid, datestamp).then((res) => {
            let data = res.data;
            this.itemList = data.programs;
            this.imgSrc = 'http://program.hndt.com' + data.image;
            this.timeSrc = data.time;
            if(data.live.length == 0){
                this.nameSrc = data.name;
            }else{
                this.nameSrc = data.live;
            }
        })
        // this.dateSrc = datestamp * 1000;
        return datestamp;
    },
    listIndex() {
        return this.top / 40 
    }
  },
  methods:{
    slideList(index) {
        this.showBtn.splice(index,1,!this.showBtn[index])
    },
    scrollTo(event){
        event.preventDefault()
        let top = event.wheelDelta; 
        let len = this.itemList.length;
        var listWrap = $('.listwrap');
        
        if(top > 0){
            this.top = this.top - 40;
        }else if(top < 0){
            this.top = this.top + 40;
        }  
        if(this.top < 0){
            this.top = 0
        }   
        if(this.top >= 40 * (len - 8)){
            this.top = 40 * (len - 8 ) ;
        }
        console.log(len ,this.top)              
        listWrap.scrollTop(this.top);
    },
    selectYear(year){
        this.year = year + '年';
        this.yearBtn = false;
    },
    selectMonth(month){
        if(month < 10){
            month = '0' + month;
        }
        this.month = month + '月'
        this.monthBtn = false;
    },
    selectDay(day){
        if(day < 10){
            day = '0' + day;
        }
        this.day = day + '日'
        this.dayBtn = false;
    },
    //直播频道选择
    isActive(cid){
        $('.loading').css('display','block');
        this.options.value = 80;
        this.cid  = cid;
        this.year = new Date().getFullYear() + '年';
        this.month = new Date().getMonth() + 1 + '月';
        this.day = new Date().getDate() + '日';
        getChannelItem(cid).then((res) => {
            let data = res.data;
            this.itemList = data.programs;
            this.imgSrc = 'http://program.hndt.com' + data.image;
            this.timeSrc = data.time;
            this.dateSrc = this.stamp * 1000;
            if(data.live.length == 0){
                this.nameSrc = data.name;
            }else{
                this.nameSrc = data.live;
            }
            this.audioSrc = data.streams[0];
            showPlayer(this.audioSrc)
            this.$nextTick(() => {
                this.top = 0;
                this.isPlayIndex()
                $('.listwrap').scrollTop(this.top - 120);
            })            
        })
    },
    //时间转时间戳
    timeToStamp(date){
        // var date = '2015-03-05 17:59:00.0';
        date = date.substring(0,19);    
        date = date.replace(/-/g,'/'); 
        var timestamp = new Date(date).getTime();
        return timestamp/1000;
    },
    //时间戳转时间
    stampTotime(value){
        var val = parseInt(value) * 1000;
        var time = new Date(val);
        var hour = time.getHours();
        var min = time.getMinutes();
        if(hour < 10){
            hour = '0' + hour;
        }
        if(min < 10){
            min = '0' + min
        }
        return hour + ':' + min;
    },
    //判断直播所在位置
    isPlayIndex(){
        var time = (new Date()).getTime()/1000;
        // console.log(time)
        var data = this.itemList;
        let len = data.length;
        var index=0;
        for(let i =0 ;i < len ; i++){
            let item = data[i];
            if(item.beginTime < time && item.endTime > time){
                index = i;
                this.activeItemIndex = i;
                this.top = index * 40;
                // console.log(this.top)
                return;
            }
        }
    },
    //点播
    selectItem(index,playUrl,title,beginTime,endTime){
        this.activeItemIndex = index; 
        this.top = index * 40;
        // console.log(playUrl)
        $('.listwrap').scrollTop(this.top - 120);
        if(playUrl && playUrl.length > 0){
            $('.loading').css('display','block');
            this.options.value = 80;
            this.audioSrc = playUrl[0];
            this.nameSrc = title;
            this.dateSrc = this.stamp * 1000;
            this.timeSrc = this.stampTotime(beginTime) + '-' + this.stampTotime(endTime);
            this.$nextTick(function(){})
            showPlayer(this.audioSrc)
        }else{
            return 
        }
    },
    //设置背景色
    setBgColor(color) {
        $('.g-mnc').css('backgroundColor',color);
        $('.item-hd').css('backgroundColor',color);
        $('.item-ft').css('backgroundColor',color);
    }
  }
}
</script>

<style scoped lang="stylus">
@keyframes rotate
    0%
        transform rotate(0deg)
    100%
        transform rotate(360deg)
@keyframes armrotate
    0%
        transform rotate(0deg)
    50%
        transform rotate(-9deg)
    100%
        transform rotate(0deg)
body
    background #f8f8f8
#app
    position: absolute
    top 0
    bottom 0
    left 0
    right 0
    // background #f8f8f8
    .g-logo
        position: absolute
        z-index: 5
        right 0
        top 40px
    .g-bg-color
        position: absolute
        z-index: 5
        right 14px
        top 120px
        .bg-item
            display inline-block
            width 18px
            height 18px
            border 1px solid #999
            margin-right 6px
            cursor pointer
    .g-sd
        position: fixed
        top 0 
        left 0
        right 160px
        bottom 0
        width 160px
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
                background-color #f8f8f8
                margin-top 1px
                cursor: pointer
                &:hover
                    background-color rgba(255, 255, 255, .92)
                &:active
                    background-color rgba(255, 255, 255, .75)                
                .icon-down,.icon-right
                    position: absolute
                    line-height 40px
                    right 24px
                    font-size 18px
                .icon-right
                    font-size 12px
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
                        background #0080cc
                    &.isActive
                        background #0080cc                    
    .g-mn
        width 100%
        height 100%
        min-width 1080px
        .g-mnc
            position: fixed
            left 160px
            right 0
            top 0
            bottom 0 
            min-width 1080px           
            background url('./imgs/bg.png') center center no-repeat
            backgorund-size cover
            background-color #f8f8f8
            .m-daylist
                position: absolute
                left 100px
                top 0
                bottom 0
                margin auto 0
                width 450px
                height 480px
                .liveItemInfo
                    margin-top 40px
                    .m-txt
                        font-size 16px
                        margin-bottom 10px
                        .lising
                            color #fff
                            padding 4px 12px
                            border-radius 20px
                            background #1ba2ff
                    .m-time
                        margin-left 10px
                        font-size 15px
                        color #000
                    .m-item
                        margin-left 100px
                        // padding 6px 10px
                        font-size 16px
                        color #000
                        font-weight 500
                        // background #333
                        // color #fff
                .m-datepick
                    margin-top 0px
                    .item
                        position: relative
                        z-index 10
                        float left
                        height 30px
                        line-height 30px
                        margin-right 20px
                        .ipt
                            vertical-align top
                            width 90px  
                            height 30px
                            line-height 30px
                            text-indent 16px
                            background #ddd
                            border 1px solid #999
                            box-sizing border-box
                            font-size 15px
                            color #666 
                        .select
                            display inline-block
                            width 30px
                            height 30px
                            line-height 30px
                            text-align center
                            color #fff
                            font-size 12px
                            background-color #444
                            cursor: pointer
                            .white
                                font-size 16px
                                line-height 30px
                        .yearBox,.monthBox,.dayBox
                            position: absolute
                            top 100%
                            width 100%
                            font-size 15px
                            color #666
                            text-indent 16px
                            background-color #ddd
                            border 1px solid #999
                            box-sizing border-box
                            border-top none 
                            height 200px
                            overflow auto
                            li
                                cursor: pointer
                                &:hover
                                    background #0080cc
                                    color #fff
                        &:nth-child(2),&:nth-child(3)
                            .ipt
                                width 70px   
                .m-itemlist
                    position: relative
                    width 395px
                    height 320px                
                    margin-top 40px
                    .item-hd
                        position: absolute
                        // z-index: 1
                        width 100%
                        height 40px
                        top 0
                        opacity: 0.75
                        background #f8f8f8
                    .item-ft
                        position: absolute
                        // z-index 1
                        width 100%
                        bottom 0
                        height 40px
                        opacity 0.75
                        background #f8f8f8
                    .item-middle
                        position: absolute
                        top 120px
                        width 100%
                        height 40px
                        background #222
                    .listwrap
                        position: relative
                        z-index: 1
                        width 100%
                        height 320px
                        overflow-y auto
                        overflow-x hidden
                        box-sizing border-box
                        ul
                            // padding 120px 0 160px
                            .list-item
                                width 380px
                                height 40px
                                line-height 40px
                                text-align center
                                font-size 16px 
                                font-weight 500
                                color #333
                                cursor: pointer
                                overflow hidden
                                &:hover
                                    background rgba(0, 0, 0, .05)
                                .list-time
                                    display inline-block
                                    width 110px
                                    text-align center
                                .list-title
                                    display inline-block
                                    width 225px
                                    white-space nowrap
                                    overflow hidden
                                    text-overflow: ellipsis
                                    text-align center
                                    box-sizing border-box
                                    padding 0 4px
                                    vertical-align middle
                                .play-icon
                                    display inline-block
                                    width 26px
                                    height 26px
                                    margin-top 8px
                                    vertical-align -8px
                                    background url('./imgs/play.png') center center no-repeat
                                    background-size cover
                                &.listIsActive
                                    color #fff
                                    font-size 18px
                                    background #333
            .m-disc
                position: absolute
                z-index: 10
                top 0 
                bottom 0
                left 6.22rem
                height 6.22rem
                margin auto 0
                .disc-bg
                    position: absolute
                    left 1.06rem
                    top 1rem
                    z-index: -1
                    width 6.7rem
                    height 6.7rem
                    background url('./imgs/shadow.png') center center no-repeat
                    background-size cover
                .disc-wrap
                    -webkit-animation rotate 12s linear infinite
                    -moz-animation rotate 12s linear infinite
                    -o-animation rotate 12s linear infinite
                    animation rotate 12s linear infinite
                    width 6.22rem
                    height 6.22rem
                    line-height 6.22rem
                    text-align center
                    background url('./imgs/disc.png') center center no-repeat
                    background-size cover
                    .disc-img
                        vertical-align middle
                        width 2.35rem
                        border-radius 50%
                .disc-arm
                    position: absolute
                    top 0.1rem
                    right -0.48rem
                    width 1.47rem
                    height 3.41rem
                    transform-origin top right
                    animation armrotate 24s linear infinite
                    background url('./imgs/tone-arm.png') center center no-repeat
                    background-size cover
    .g-play
        position: absolute
        z-index: 5
        right 80px
        bottom 40px
        // min-width 200px
        .m-voice
            display inline-block
            width 27px
            height 23px
            background url('./imgs/voice.png') center center no-repeat
            background-size cover
        .m-time
            margin-left 10px
            font-size 14px
            color #666
        .m-item
            margin-left 10px
            padding 6px 10px
            font-size 16px
            background #333
            color #fff
        .volume
            position: absolute
            right -50px
            bottom -4px
            width 46px
            height 50px
            background url('./imgs/volume.png') center bottom no-repeat
            background-size 30px 30px
            cursor: pointer
            .vo-slide
                position: absolute
                bottom 28px
                left 10px
    .audio
        display none
</style>

<template>
  <div id="app">
    <div class="g-logo">
        <img src="./imgs/logo.png">
    </div>
    <div class="g-sd">
      <div class="m-list">
          <div class="list-name" @click="slideList(0)">
              河南电台
              <i class="icon-down"></i>
          </div>
          <ul class="list-box" v-show="showBtn[0]">
              <li class="list-item" v-for="item of hnItemList" @click="isActive(item.cid)" :class="{isActive: cid == item.cid ? true : false}">{{item.name}}</li>
          </ul>
      </div>
      <div class="m-list">
          <div class="list-name" @click="slideList(1)">
              网络电台
              <i class="icon-down"></i>
          </div>
          <ul class="list-box" v-show="showBtn[1]">
              <li class="list-item" v-for="item of wlItemList" @click="isActive(item.cid)" :class="{isActive: cid == item.cid ? true : false}">{{item.name}}</li>
          </ul>
      </div>
      <div class="m-list">
          <div class="list-name" @click="slideList(2)">
              地市台
              <i class="icon-down"></i>
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
                <div class="item-middle"></div>
                <div class="listwrap">
                    <ul @mousewheel='scrollTo($event)'>
                        <li class="list-item" v-for="(item,index) of itemList" @click="selectItem(index,item.playUrl)">            
                            <span class="list-time">
                                {{item.beginTime |formdata}} - {{item.endTime |formdata}}
                            </span>
                            <span class="list-title">{{item.title}}</span>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
        <div class="m-disc">
            <div class="disc-bg"></div>
            <div class="disc-wrap">
                <img :src="imgSrc" alt="" class="disc-img">
            </div>
            <div class="disc-arm"></div>
        </div>
      </div>
    </div>
    <div class="g-play">
        <!-- <span class="m-voice"></span> -->
        <wvoice></wvoice>
        <span class="m-time">
            {{dateSrc | formdate}}  {{timeSrc}}    
        </span>
        <span class="m-item" v-html="nameSrc">
        </span>
    </div>
    <span style="display: none">{{stamp}}</span>
  </div>
</template>

<script>
import { getLiveItem, getClassItem, getChannelItem, clickItem } from '@/api.js'
import voice from './voice.vue'
const years = [
    {id:2017},
    {id:2016},
]
export default {
  name: 'app',
  components:{
    wvoice:voice
  },  
  data() {
    return {
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
        cid:1,
        player:null
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
            $('.listwrap').scrollTop(this.top);
        })
        
    })
    this.dateSrc =(new Date()).getTime();
  },
  mounted(){

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
        this.dateSrc = datestamp * 1000;
        return datestamp;
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
        if(this.top < 0){
            this.top = 0
        }   
        if(this.top > 40 * len){
            this.top = 40 * len;
        }       
        if(top > 0){
            this.top = this.top - 40;
        }else if(top < 0){
            this.top = this.top + 40;
        }         
        $('.listwrap').scrollTop(this.top);
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
        this.cid  = cid;
        getChannelItem(cid).then((res) => {
            let data = res.data;
            this.itemList = data.programs;
            this.imgSrc = 'http://program.hndt.com' + data.image;
            this.timeSrc = data.time;
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
                $('.listwrap').scrollTop(this.top);
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
                this.top = index * 40;
                return;
            }
        }
    },
    //点播
    selectItem(index,playUrl){
        this.top = index * 40;
        $('.listwrap').scrollTop(this.top);
        if(playUrl && playUrl.length > 0){
            this.audioSrc = playUrl[0];
            this.$nextTick(function(){})
        } 
        showPlayer(this.audioSrc)
    },
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
    background #f8f8f8
    .g-logo
        position: absolute
        z-index: 5
        right 0
        top 40px
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
                height 400px
                .m-datepick
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
                    width 382px
                    height 320px                
                    margin-top 40px
                    .item-hd
                        position: absolute
                        width 100%
                        height 40px
                        top 0
                        background rgba(248, 248, 248, .8)
                    .item-ft
                        position: absolute
                        width 100%
                        bottom 0
                        height 40px
                        background rgba(248, 248, 248, .8)
                    .item-middle
                        position: absolute
                        // z-index -1
                        top 120px
                        width 100%
                        height 40px
                        background #ffea02
                    .listwrap
                        position: relative
                        width 100%
                        height 320px
                        overflow-y auto
                        overflow-x hidden
                        ul
                            padding 120px 0 160px
                            .list-item
                                width 380px
                                height 40px
                                line-height 40px
                                text-align center
                                font-size 16px 
                                font-weight 500
                                color #333
                                cursor: pointer
                                .list-time
                                    display inline-block
                                    width 120px
                                    text-align center
                                .list-title
                                    display inline-block
                                    width 250px
                                    // white-space nowrap
                                    // overflow hidden
                                    // text-overflow: ellipsis
                                    text-align center
            .m-disc
                position: absolute
                top 0 
                bottom 0
                left 550px
                height 7rem
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
                    -webkit-animation rotate 6s linear infinite
                    -moz-animation rotate 6s linear infinite
                    -o-animation rotate 6s linear infinite
                    animation rotate 6s linear infinite
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
                    animation armrotate 20s linear infinite
                    background url('./imgs/tone-arm.png') center center no-repeat
                    background-size cover
    .g-play
        position: absolute
        z-index: 5
        right 40px
        bottom 40px
        min-width 200px
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
    .audio
        display none
</style>

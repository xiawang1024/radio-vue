<template>
  <div class="loader-inner line-scale-pulse-out-rapid">
      <div></div>
      <div></div>
      <div></div>
      <div></div>
  </div>
</template>

<script>

export default {
  name: 'app',
  data() {
    return {
        
    }
  },
  computed:{
    
  },
  methods:{
   
  }
}
</script>

<style scoped>
.loader-inner{
    position: relative;
    /*top: 17px;*/
    height: 30px;
    overflow: hidden;
    width:44px;
    display: inline-block;
}
@-webkit-keyframes line-scale-pulse-out-rapid {
  0% {
    -webkit-transform: scaley(1);
            transform: scaley(1); }

  80% {
    -webkit-transform: scaley(0.3);
            transform: scaley(0.3); }

  90% {
    -webkit-transform: scaley(1);
            transform: scaley(1); } }

@keyframes line-scale-pulse-out-rapid {
  0% {
    -webkit-transform: scaley(1);
            transform: scaley(1); }

  80% {
    -webkit-transform: scaley(0.3);
            transform: scaley(0.3); }

  90% {
    -webkit-transform: scaley(1);
            transform: scaley(1); } }

.line-scale-pulse-out-rapid > div {
  background-color: #1ba2ff;
  width: 4px;
  height: 50px;
  border-radius: 2px;
  margin: 2px;
  -webkit-animation-fill-mode: both;
          animation-fill-mode: both;
  display: inline-block;
  -webkit-animation: line-scale-pulse-out-rapid 0.9s 0s infinite cubic-bezier(.11, .49, .38, .78);
          animation: line-scale-pulse-out-rapid 0.9s 0s infinite cubic-bezier(.11, .49, .38, .78); }
  .line-scale-pulse-out-rapid > div:nth-child(2), .line-scale-pulse-out-rapid > div:nth-child(4) {
    -webkit-animation-delay: 0.25s !important;
            animation-delay: 0.25s !important; }
  .line-scale-pulse-out-rapid > div:nth-child(1), .line-scale-pulse-out-rapid > div:nth-child(5) {
    -webkit-animation-delay: 0.5s !important;
            animation-delay: 0.5s !important; }

</style>

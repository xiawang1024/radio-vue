<template>
  <div id="app">
    <player></player>
    <audio id="audio" autoplay width="0" height="0"></audio>
  </div>
</template>
<script>
import Player from '@/components/player'
export default {
  name: "app",
  data () {
    return {

    }
  },
  components: {
    Player
  }
}
</script>
<style lang="stylus">
</style>

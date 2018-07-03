<template>
  <div class="hello">
    <echart-map v-bind:coords="coords" v-bind:orbit="orbit"></echart-map>
  </div>
</template>

<script>
  import EchartMap from './EchartMap'

  export default {
    name: 'HelloWorld',
    data() {
      return {
        msg: 'Welcome to Your Vue.js App',
        coords: [
          {fromCoord: '上海', toCoord: '呼和浩特'},
          {fromCoord: '呼和浩特', toCoord: '沈阳'},
          {fromCoord: '沈阳', toCoord: '长春'},
          {fromCoord: '广州', toCoord: '北京'},
          {fromCoord: '武汉', toCoord: '南京'},
          {fromCoord: '昆明', toCoord: '西安'}
        ],
        orbit: {
          a: 11,
          c: 3
        }
      }
    },
    components: {
      EchartMap
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h1, h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }
</style>

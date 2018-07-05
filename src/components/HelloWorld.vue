<template>
  <div class="hello">
    <div>
      <div>
        <ul>
          <li v-for="item in menuList" v-bind:key="item.name" v-on:click="setMenu(item)">{{item.name}}</li>
        </ul>
      </div>
      <div>
        <echart-map ref="map" v-bind:coords="coords" v-bind:orbit="orbit"></echart-map>
      </div>
    </div>
  </div>
</template>

<script>
  import EchartMap from './EchartMap'

  export default {
    name: 'HelloWorld',
    data() {
      return {
        msg: 'Welcome to Your Vue.js App',
        coords: [],
        orbit: {
          a: 11,
          c: 3,
          aa: {
            dd: 'aaa'
          }
        },
        menuList: [
          {
            name: 'Menu A',
            coords: [
              {fromCoord: '长沙', toCoord: '呼和浩特'},
              {fromCoord: '长沙', toCoord: '沈阳'},
              {fromCoord: '长沙', toCoord: '长春'},
              {fromCoord: '长沙', toCoord: '北京'},
              {fromCoord: '长沙', toCoord: '南京'},
              {fromCoord: '长沙', toCoord: '西安'}
            ]
          },
          {
            name: 'Menu B',
            coords: [
              {fromCoord: '乌鲁木齐', toCoord: '重庆'},
              {fromCoord: '乌鲁木齐', toCoord: '兰州'},
              {fromCoord: '乌鲁木齐', toCoord: '拉萨'},
              {fromCoord: '乌鲁木齐', toCoord: '哈尔滨'},
              {fromCoord: '乌鲁木齐', toCoord: '济南'},
              {fromCoord: '乌鲁木齐', toCoord: '福州'}
            ]
          }
        ]
      }
    },
    components: {
      EchartMap
    },
    methods: {
      setMenu(item) {
        // console.log(item)
        this.coords = item.coords
      }
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

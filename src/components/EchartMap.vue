<template lang="pug">
  div.map(ref="myMap")
</template>

<script type="text/ecmascript-6">
  import geoCoordMap from './geoCoord'
  import _ from 'lodash'

  export default {
    props: {
      // 迁出地和迁入地
      coords: {
        type: Array,
        default: () => {
          return []
        }
      },
      // 迁徙轨迹
      orbit: {
        type: Object,
        default: () => {
          return {}
        }
      },
      // 目的地
      destination: {
        type: Object,
        default: () => {
          return {}
        }
      },
      // 起点
      origin: {
        type: Object,
        default: () => {
          return {}
        }
      },
      // 迁徙标记
      orbitPath: {
        type: String,
        default: 'path://M857.3 655.252l-345.297-552.56-345.299 552.56 161.09 0 0 266.058 368.417 0L696.211 655.252 857.3 655.252z'
      },
      // 目的地标记
      destinationPath: {
        type: String,
        default: 'path://M565.272827 34.627285l112.095872 237.542288c8.706637 18.321022 25.411424 31.051641 44.82285 33.996289l250.776598 38.081157c48.697387 7.411435 68.22505 70.046082 32.933559 105.979639l-181.494353 184.937155c-13.998147 14.230618-20.352386 34.815477-17.05903 54.93539l42.819161 261.127145c8.346858 50.695541-42.64204 89.451974-86.225039 65.51841l-224.307979-123.271141c-17.285968-9.525824-37.992596-9.525824-55.278564 0l-224.313514 123.271141c-43.582999 23.933565-94.571897-14.822869-86.219504-65.51841l42.813626-261.127145c3.321031-20.119914-3.088559-40.704772-17.086706-54.93539l-181.439002-184.937155c-35.285956-35.933557-15.819179-98.57374 32.933559-105.979639l250.748923-38.081157c19.350541-2.939112 36.083003-15.675267 44.75643-33.996289l112.123547-237.542288C480.497972-11.540583 543.509003-11.540583 565.272827 34.627285z'
      },
      // 起点标记
      originPath: {
        type: String,
        default: 'path://M565.272827 34.627285l112.095872 237.542288c8.706637 18.321022 25.411424 31.051641 44.82285 33.996289l250.776598 38.081157c48.697387 7.411435 68.22505 70.046082 32.933559 105.979639l-181.494353 184.937155c-13.998147 14.230618-20.352386 34.815477-17.05903 54.93539l42.819161 261.127145c8.346858 50.695541-42.64204 89.451974-86.225039 65.51841l-224.307979-123.271141c-17.285968-9.525824-37.992596-9.525824-55.278564 0l-224.313514 123.271141c-43.582999 23.933565-94.571897-14.822869-86.219504-65.51841l42.813626-261.127145c3.321031-20.119914-3.088559-40.704772-17.086706-54.93539l-181.439002-184.937155c-35.285956-35.933557-15.819179-98.57374 32.933559-105.979639l250.748923-38.081157c19.350541-2.939112 36.083003-15.675267 44.75643-33.996289l112.123547-237.542288C480.497972-11.540583 543.509003-11.540583 565.272827 34.627285z'
      }
    },
    data() {
      return {
        originData: [],
        series: []
      }
    },
    created() {
      // 迁徙轨迹
      this.orbitConfig = _.merge({
        type: 'lines',
        zLevel: 2,
        effect: {
          show: true,
          period: 5,
          trailLength: 0,
          symbol: this.orbitPath,
          symbolSize: [15, 25],
          color: '#003dba'
        },
        lineStyle: {
          normal: {
            width: 4,
            opacity: 1,
            curveness: 0,
            color: '#37cb00'
          }
        }
      }, this.orbit)
      // 目的地
      this.destinationConfig = _.merge({
        type: 'scatter',
        coordinateSystem: 'geo',
        zLevel: 2,
        rippleEffect: {
          period: 4,
          brushType: 'stroke',
          scale: 4
        },
        label: {
          normal: {
            show: true,
            position: 'right',
            offset: [-24, -18],
            formatter: '{b}',
            fontWeight: 'lighter',
            fontSize: 12,
            color: '#000000'
          },
          emphasis: {
            show: false
          }
        },
        symbol: this.destinationPath,
        symbolSize: 20,
        itemStyle: {
          normal: {
            show: false,
            color: '#9200a8'
          }
        }
      }, this.destination)
      // 出发地
      this.originConfig = _.merge({
        type: 'scatter',
        coordinateSystem: 'geo',
        zLevel: 2,
        rippleEffect: {
          period: 4,
          brushType: 'stroke',
          scale: 4
        },
        label: {
          normal: {
            show: true,
            formatter: '{b}',
            color: '#FFFF33'
          }
        },
        symbol: this.originPath,
        symbolSize: 20,
        symbolKeepAspect: true,
        itemStyle: {
          color: '#ff0000'
        }
      }, this.origin)
    },
    mounted() {
      let originNames = []
      this.originData = []
      this.coords.map(item => {
        originNames.push(item.fromCoord)
      })
      originNames = _.uniq(originNames)
      originNames.forEach(item => {
        this.originData.push(geoCoordMap[item])
      })
      this.initChart()
    },
    updated() {
      if (!this.chart) {
        this.initChart()
      }
    },
    beforeDestroy() {
      if (!this.chart) {
        return
      }
      this.chart.dispose()
      this.chart = null
    },
    methods: {
      initChart() {
        this.chart = window.echarts.init(this.$refs.myMap)
        this.getSeries()
        this.chart.setOption(this.opt)
      },
      convertData(data) {
        let res = []
        for (let i = 0; i < data.length; i++) {
          let dataItem = data[i]
          let fromCoord = geoCoordMap[dataItem.fromCoord]
          let toCoord = geoCoordMap[dataItem.toCoord]
          if (fromCoord && toCoord) {
            res.push([
              {
                coord: fromCoord
              },
              {
                coord: toCoord
              }
            ])
          }
        }
        return res
      },
      getSeries() {
        let series = []
        let dd = [this.coords]
        dd.forEach((item) => {
          this.orbitConfig.data = this.convertData(item)
          this.destinationConfig.data = item.map((dataItem) => {
            let arr = {
              name: dataItem.toCoord,
              value: geoCoordMap[dataItem.toCoord]
            }
            return arr
          })
          this.originConfig.data = this.originData
          series.push(
            // 迁徙轨迹
            this.orbitConfig,
            // 目标地
            this.destinationConfig,
            // 出发地
            this.originConfig
          )
        })
        this.series = series
      }
    },
    computed: {
      opt() {
        let obj = {
          geo: {
            map: 'china',
            label: {
              emphasis: {
                show: false,
                fontWeight: 100,
                fontSize: 20,
                rotate: 90,
                color: '#333333',
                position: ['50%', '50%'],
                backgroundColor: '#fff',
                borderColor: '#ff0000',
                borderWidth: 1,
                borderRadius: 5,
                padding: 10,
                shadowColor: 'rgba(0, 0, 0, 1)',
                shadowBlur: 10,
                width: '200px',
                height: 180
              }
            },
            // aspectScale: 0.5,
            // zoom: 1.5,
            layoutCenter: ['50%', '50%'],
            layoutSize: '100%',
            itemStyle: {
              normal: {
                color: '#CCFFFF',
                borderWidth: 1,
                opacity: 1,
                shadowColor: 'rgba(0, 0, 0, 0.5)',
                shadowBlur: 10
              },
              emphasis: {
                color: '#CCCC33'
              }
            }
          },
          series: this.series
        }
        return obj
      }
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  .map
    width 800px
    height 800px
    border 1px solid #000
    margin 0 auto
</style>

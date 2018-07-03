<template lang="pug">
  div.map(ref="myMap")
</template>

<script type="text/ecmascript-6">
  import geoCoordMap from './geoCoord'
  import _ from 'underscore'

  let planePath = 'path://M275.2 864c0 0 128-102.4 236.8-108.8 108.8 6.4 236.8 108.8 236.8 108.8-6.4-108.8-83.2-179.2-83.2-179.2C761.6 320 505.6 0 505.6 0S256 320 358.4 684.8C358.4 684.8 275.2 761.6 275.2 864zM473.6 192l64 0c0 0 83.2 0 83.2 192L396.8 384C390.4 192 473.6 192 473.6 192zM576 793.6l-70.4-12.8-70.4 12.8c0 0-44.8 19.2-38.4 44.8 6.4 25.6 57.6 153.6 102.4 179.2 44.8-19.2 96-147.2 102.4-179.2C620.8 812.8 576 793.6 576 793.6z'
  let bugPath = 'path://M969.142857 548.571429q0 14.848-10.861714 25.709714t-25.709714 10.861714l-128 0q0 97.718857-38.290286 165.705143l118.857143 119.442286q10.861714 10.861714 10.861714 25.709714t-10.861714 25.709714q-10.276571 10.861714-25.709714 10.861714t-25.709714-10.861714l-113.152-112.566857q-2.852571 2.852571-8.557714 7.424t-23.990857 16.274286-37.156571 20.845714-46.848 16.566857-55.442286 7.424l0-512-73.142857 0 0 512q-29.147429 0-58.002286-7.716571t-49.700571-18.870857-37.705143-22.272-24.868571-18.578286l-8.557714-8.009143-104.557714 118.272q-11.446857 11.995429-27.428571 11.995429-13.714286 0-24.576-9.142857-10.861714-10.276571-11.702857-25.417143t8.850286-26.587429l115.419429-129.718857q-33.133714-65.133714-33.133714-156.562286l-128 0q-14.848 0-25.709714-10.861714t-10.861714-25.709714 10.861714-25.709714 25.709714-10.861714l128 0 0-168.009143-98.852571-98.852571q-10.861714-10.861714-10.861714-25.709714t10.861714-25.709714 25.709714-10.861714 25.709714 10.861714l98.852571 98.852571 482.304 0 98.852571-98.852571q10.861714-10.861714 25.709714-10.861714t25.709714 10.861714 10.861714 25.709714-10.861714 25.709714l-98.852571 98.852571 0 168.009143 128 0q14.848 0 25.709714 10.861714t10.861714 25.709714zM694.857143 219.428571l-365.714286 0q0-75.995429 53.430857-129.426286t129.426286-53.430857 129.426286 53.430857 53.430857 129.426286z'
  let example = 'path://M565.272827 34.627285l112.095872 237.542288c8.706637 18.321022 25.411424 31.051641 44.82285 33.996289l250.776598 38.081157c48.697387 7.411435 68.22505 70.046082 32.933559 105.979639l-181.494353 184.937155c-13.998147 14.230618-20.352386 34.815477-17.05903 54.93539l42.819161 261.127145c8.346858 50.695541-42.64204 89.451974-86.225039 65.51841l-224.307979-123.271141c-17.285968-9.525824-37.992596-9.525824-55.278564 0l-224.313514 123.271141c-43.582999 23.933565-94.571897-14.822869-86.219504-65.51841l42.813626-261.127145c3.321031-20.119914-3.088559-40.704772-17.086706-54.93539l-181.439002-184.937155c-35.285956-35.933557-15.819179-98.57374 32.933559-105.979639l250.748923-38.081157c19.350541-2.939112 36.083003-15.675267 44.75643-33.996289l112.123547-237.542288C480.497972-11.540583 543.509003-11.540583 565.272827 34.627285z'

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
        default: {}
      }
    },
    data() {
      return {
        origin: [],
        series: []
      }
    },
    created() {
    },
    mounted() {
      const myObj = {a: 1, b: 2}
      Object.assign(myObj, this.orbit)
      console.log(myObj)
      let originNames = []
      let originDates = []
      this.coords.map(item => {
        originNames.push(item.fromCoord)
      })
      originNames = _.uniq(originNames)
      originNames.forEach(item => {
        originDates.push(geoCoordMap[item])
      })
      this.origin = originDates
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
        console.log(this.opt)
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
          console.log('item', item)
          series.push(
            // 迁徙轨迹
            {
              type: 'lines',
              zLevel: 2,
              effect: {
                show: true,
                period: 5,
                trailLength: 0,
                symbol: planePath,
                symbolSize: 25,
                color: '#000066'
              },
              lineStyle: {
                normal: {
                  width: 4,
                  opacity: 1,
                  curveness: 0,
                  color: '#c8cb09'
                }
              },
              data: this.convertData(item)
            },
            // 目标地
            {
              type: 'effectScatter',
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
                  offset: [13, 0],
                  formatter: '{b}',
                  fontWeight: 'lighter',
                  fontSize: 12,
                  color: '#000000'
                },
                emphasis: {
                  show: false
                }
              },
              symbol: example,
              symbolSize: (val) => {
                console.log('val', val)
                // return 4 + val[2] / 10
                return 20
              },
              itemStyle: {
                normal: {
                  show: false,
                  color: '#33FF33'
                }
              },
              data: item.map((dataItem) => {
                let arr = {
                  name: dataItem.toCoord,
                  value: geoCoordMap[dataItem.toCoord]
                }
                return arr
              })
            },
            // 出发地
            {
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
                  show: false,
                  color: '#33FF33'
                }
              },
              symbol: bugPath,
              symbolSize: 20,
              symbolColor: '#003333',
              itemStyle: {
                normal: {
                  show: true,
                  color: '#006633'
                }
              },
              data: this.origin
            }
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

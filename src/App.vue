<template>
  <div id="app">
    <h1>都道府県別の総人口推移グラフ</h1>
    <div class="prefectures-container">
      <h2>都道府県</h2>
      <Prefectures @onAddSeries="addSeries" @onRemoveSeries="removeSeries" />
    </div>
    <Highcharts :options="options" />
  </div>
</template>

<script>
import { Chart } from "highcharts-vue";
import Prefectures from "./Transition.vue";

export default {
  components: {
    Highcharts: Chart,
    Prefectures: Prefectures
  },
  data() {
    return {
      options: {
        series: [],
        title: {
          style: {
            display: "none"
          }
        },
        legend: {
          align: "right",
          verticalAlign: "top",
          layout: "vertical"
        },
        xAxis: {
          title: {
            text: "年度"
          },
          categories: [
            1960,
            1965,
            1970,
            1975,
            1980,
            1985,
            1990,
            1995,
            2000,
            2005,
            2010,
            2015,
            2020,
            2025,
            2030,
            2035,
            2040,
            2045,
          ]
        },
        yAxis: {
          title: {
            text: "人口数"
          }
        }
      }
    };
  },
  methods: {
    addSeries: function(id, name, population) {
      this.options.series.push({
        id: id,
        name: name,
        data: population
      });
    },
    removeSeries: function(id) {
      this.options.series = this.options.series.filter(val => val.id !== id);
    }
  }
};
</script>

<style scoped>
#app {
  max-width: 1024px;
  margin: 0 auto;
}

h1 {
  text-align: center;
  font-size: 20px;
}

.prefectures-container {
  margin-left: 4%;
}

h2 {
  font-size: 17px;
}
</style>
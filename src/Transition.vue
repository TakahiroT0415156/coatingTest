<template>
  <div class="prefectures-area">
    <div v-for="prefecture in prefectures" :key="prefecture.id" class="prefecture">
      <label :for="prefecture.id">
        <input
          type="checkbox"
          :id="prefecture.id"
          :checked="prefecture.isChecked"
          @click="switchChart(prefecture.id, prefecture.name, prefecture.isChecked)"
        />
        {{ prefecture.name }}
      </label>
    </div>
  </div>
</template>

<script>
import axios from "axios";

const ACCESS_TOKEN = process.env.VUE_APP_RESAS_API;

export default {
  data() {
    return {
      prefectures: []
    };
  },
  mounted() {
    this.initPrefectures();
  },
  methods: {
    // APIにアクセス
    fetchAPI: function(path) {
      // sample GET https://opendata.resas-portal.go.jp/api/v1/prefectures
      const response = axios.get(
        `https://opendata.resas-portal.go.jp/api/v1/${path}`,
        {
          headers: { "X-API-KEY": ACCESS_TOKEN }
        }
      );
      return response;
    },
    initPrefectures: async function() {
      // 初期値の設定
      const path = "prefectures";
      try {
        const response = await this.fetchAPI(path);
        this.prefectures = response.data.result.map(val => {
          return {
            id: val["prefCode"],
            name: val["prefName"],
            isChecked: false
          };
        });
      } catch (error) {
        console.error(error.message);
      }
    },
    drawChart: async function(id, name) {
      // /result/prefCode 都道府県コード
      // /result/prefName 都道府県名
      const path = `population/composition/perYear?prefCode=-&prefCode=${id}`;
      try {
        const response = await this.fetchAPI(path);
        const population = response.data.result.data[0].data.map(
          val => val["value"]
        );
        this.$emit("onAddSeries", id, name, population);
        this.prefectures[id - 1].isChecked = true;
      } catch (error) {
        console.error(error.message);
      }
    },
    deleteChart: function(id) {
      this.$emit("onRemoveSeries", id);
      this.prefectures[id - 1].isChecked = false;
    },
    switchChart: function(id, name, isChecked) {
      if (isChecked) {
        this.deleteChart(id);
      } else {
        this.drawChart(id, name);
      }
    }
  }
};
</script>

<style scoped>
  .prefectures-area {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
  }

  .prefectures {
    font-size: 15px;
  }

  label {
    cursor: pointer;
  }
</style>
<template>
  <div class="canvas-wrapper">
    <Line v-if="loaded" :data="chartData" />
  </div>
</template>

<script lang="ts">
import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend,
} from 'chart.js';
import { Line } from 'vue-chartjs';

ChartJS.register(CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend);

export default {
  name: 'App',
  components: {
    Line,
  },
  data: () => ({
    loaded: false,
    chartData: {},
  }),
  async mounted() {
    try {
      this.loaded = false;

      const response = await fetch('https://localhost:7020/DailyInfo/GetKeyRate');
      const data = await response.json();
      const labels = data.keyRates.map((item: { date: Date }) => {
        return new Date(item.date).toLocaleString('ru', {
          year: 'numeric',
          month: 'numeric',
          day: 'numeric',
        });
      });
      const datasets = data.keyRates.map((item: { rate: number }) => {
        return item.rate;
      });

      this.chartData = {
        labels: labels,
        datasets: [
          {
            label: 'Ключевая ставка ЦБ',
            backgroundColor: '#f87979',
            data: datasets,
          },
        ],
      };

      this.loaded = true;
    } catch (error) {
      console.log(error);
    }
  },
};
</script>

<style>
.canvas-wrapper {
  display: grid;
  grid-area: B;
  width: 100%;
}
</style>

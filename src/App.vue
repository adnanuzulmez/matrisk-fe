<template>
  <div style="width: 100%; height: 100%;">
   <Chart/>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { use } from "echarts/core";
import VChart from "vue-echarts";
import { CandlestickChart } from "echarts/charts";
import { TooltipComponent, GridComponent, LegendComponent } from "echarts/components";
import { CanvasRenderer } from "echarts/renderers";
import Chart from "./components/Chart.vue";

use([CandlestickChart, TooltipComponent, GridComponent, LegendComponent, CanvasRenderer]);

// Chart options
const chartOptions = ref({
  title: {
    text: "Candlestick Chart Example",
    left: "center",
  },
  tooltip: {
    trigger: "axis",
    axisPointer: {
      type: "cross",
    },
  },
  legend: {
    data: ["Daily Data"],
    left: "right",
  },
  grid: {
    left: "10%",
    right: "10%",
    bottom: "15%",
  },
  xAxis: {
    type: "category",
    data: ["2024-11-18", "2024-11-19", "2024-11-20", "2024-11-21", "2024-11-22"],
    scale: true,
    boundaryGap: true,
    axisLine: { onZero: false },
    splitLine: { show: false },
    splitNumber: 10,
  },
  yAxis: {
    scale: true,
    splitArea: {
      show: true,
    },
  },
  series: [
    {
      name: "Daily Data",
      type: "candlestick",
      data: [
    [20, 30, 10, 35], // [Open, Close, Lowest, Highest]
    [40, 35, 30, 45],
    [33, 38, 30, 40],
    [40, 50, 35, 55],
    [38, 33, 25, 45],
  ], // Candlestick veri seti buraya gelecek
    },
  ],
});

// Veri yükleme
onMounted(() => {
  
  
});
</script>

<style scoped>
/* Kapsayıcı stil */
div {
  width: 100%;
  height: 400px;
}
</style>
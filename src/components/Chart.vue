<template>
  <div style="width: 100%; height: 100%">
    <Button @click="getData('daily')">Günlük</Button>
    <Button @click="getData('monthly')">Aylık</Button>
    <Button @click="getData('hourly')">Saatlik</Button>

    <!-- Candlestick Chart -->
    <v-chart
      :option="chartOptions"
      style="width: 100%; height: 400px"
      autoresize
    />
    <v-chart :option="chartOptionLine" style="width: 100%; height: 400px" autoresize />

    <!-- Excel Yükle Butonu -->
    <Button
      label="Excel Yükle"
      icon="pi pi-upload"
      class="p-button-success"
      @click="openFileDialog"
    />

    <!-- Gizli Dosya Input -->
    <input
      type="file"
      ref="fileInput"
      @change="onFileChange"
      style="display: none"
      accept=".xlsx"
    />
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { use } from "echarts/core";
import VChart from "vue-echarts";
import { CandlestickChart,
    LineChart,

 } from "echarts/charts";
import {
  TooltipComponent,
  GridComponent,
  LegendComponent,
  DataZoomComponent,

} from "echarts/components";
import { CanvasRenderer } from "echarts/renderers";
import Button from "primevue/button";
import ExcelJS from "exceljs";
import * as XLSX from "xlsx";

use([
  CandlestickChart,
  TooltipComponent,
  GridComponent,
  LegendComponent,
  CanvasRenderer,
  DataZoomComponent,
    LineChart,
]);

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
  dataZoom: [
    {
      id: "dataZoomX",
      type: "inside",
      xAxisIndex: [0],
      filterMode: "empty",
    },
    {
      id: "dataZoomXSlider",
      type: "slider",
      xAxisIndex: [0],
      filterMode: "filter",
    },
  ],
  xAxis: {
    type: "category",
    data: [],
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
      data: [], // Candlestick veri seti buraya gelecek
      large: true,
    },
  ],
});

const chartOptionLine = ref({
  title: {
    text: "Line Chart Example",
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
  dataZoom: [
    {
      id: "dataZoomX",
      type: "inside",
      xAxisIndex: [0],
      filterMode: "empty",
    },
    {
      id: "dataZoomXSlider",
      type: "slider",
      xAxisIndex: [0],
      filterMode: "filter",
    },
  ],
  xAxis: {
    type: "category",
    data: [],
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
      type: "line",
      data: [], // Candlestick veri seti buraya gelecek
      large: true,
    },
  ],
});

const chartOptionsMdiPdi = ref({
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
  dataZoom: [
    {
      id: "dataZoomX",
      type: "inside",
      xAxisIndex: [0],
      filterMode: "empty",
    },
    {
      id: "dataZoomXSlider",
      type: "slider",
      xAxisIndex: [0],
      filterMode: "filter",
    },
  ],
  xAxis: {
    type: "category",
    data: [],
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
      data: [], // Candlestick veri seti buraya gelecek
    },
  ],
});

// Referanslar
const fileInput = ref(null);

// Dosya Seçme Penceresini Aç
const openFileDialog = () => {
  fileInput.value.click();
};

// Dosya Yüklenince Çalışan Fonksiyon
const onFileChange = async (e) => {
  const file = e.target.files[0];
  if (!file) return;

  try {
    // Dosyayı arrayBuffer olarak oku
    const arrayBuffer = await file.arrayBuffer();

    // SheetJS ile dosyayı yükle
    const workbook = XLSX.read(arrayBuffer, { type: "array" });

    // İlk sayfayı al
    const sheetName = workbook.SheetNames[0]; // İlk sayfanın ismi
    const worksheet = workbook.Sheets[sheetName];

    // Sheet'i JSON formatına çevir ve konsola yazdır
    const jsonData = XLSX.utils.sheet_to_json(worksheet);
    const dates = jsonData.map((item) => item.currentBarTime); // Zaman bilgisi (currentBarTime)

    // Candlestick verilerini oluştur
    const candlestickData = jsonData.map((item) => [
      item.initialPrice, // Open
      item.currentPrice, // Close
      item.currentPrice - 0.5, // Lowest (yaklaşık)
      item.currentPrice + 0.5, // Highest (yaklaşık)
    ]);

    // Chart'a veriyi ekle
    chartOptions.value.xAxis.data = dates;
    chartOptions.value.series[0].data = candlestickData;
    console.log(jsonData);
    chartOptionLine.value.xAxis.data = dates;
    chartOptionLine.value.series[0].data = jsonData.map((item) => item.currentPrice);


  } catch (error) {
    console.error("Excel dosyası okuma hatası:", error);
  }
};
</script>

<style scoped>
/* Kapsayıcı stil */
div {
  width: 100%;
  height: 400px;
}
</style>

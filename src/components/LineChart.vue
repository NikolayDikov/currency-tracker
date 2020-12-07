<template>
  <div>
    <canvas id="currency-chart"></canvas>
  </div>
</template>
<script>
import Chart from "chart.js";
export default {
  name: "LineChart",
  props: {
    chartData: Object,
    pointLabel: String,
    today: Number,
    lineChartColors: Object,
    pointColors: Array
  },
  data() {
    return {
      
      pointColorsArr: [],
      highestPoint: null,
      currencyChartData: {
        type: "line",
        data: {
          labels: this.chartData.map((d) => d.chartDate),
          datasets: [
            {
              // one line graph
              label: this.pointLabel,
              data: this.chartData.map((d) => d.res),
              borderWidth: 3,
              pointBorderColor: this.pointColors,
              pointBackgroundColor: this.lineChartColors.yellow
              
            },
          ],
          todayEx: this.today
        },
        options: {
          responsive: true,
          tooltips: {
            callbacks: {
              label: function (tooltipItem, data) {
                let label = data.datasets[tooltipItem.datasetIndex].label || "";

                if (label) {
                  label += ": ";
                }
                label += Math.round(tooltipItem.yLabel * 100) / 100;
                return label;
              },
              afterLabel: function(tooltipItem, data) {
                let Diff = Math.round((tooltipItem.yLabel-data.todayEx) * 100) / 100;
                return 'Difference: ' + Diff + '';
              }
            },
            backgroundColor: '#FFF',
            titleFontSize: 16,
            titleFontColor: '#0066ff',
            bodyFontColor: '#000',
            bodyFontSize: 14,
            displayColors: false,
            borderWidth: 1
          },
        },
      },
    };
  },
  mounted() {
      const ctx = document.getElementById("currency-chart").getContext("2d");;
      let gradientFill = ctx.createLinearGradient(500, 0, 100, 0);
      gradientFill.addColorStop(0, "rgba(128, 182, 244, 0.6)");
      gradientFill.addColorStop(1, "rgba(244, 144, 128, 0.6)");
      this.currencyChartData.data.datasets[0]['backgroundColor']=gradientFill;
      this.createChart("currency-chart", this.currencyChartData);
  },
  methods: {
    createChart(chartId, chartData) {
      const ctx = document.getElementById(chartId);
      const myChart = new Chart(ctx, {
        type: chartData.type,
        data: chartData.data,
        options: chartData.options,
      });
    },
  },
};
</script>
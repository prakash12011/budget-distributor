<template>
  <Pie
    :chart-options="chartOptions"
    :chart-data="chartData"
    :chart-id="chartId"
    :dataset-id-key="datasetIdKey"
    :plugins="plugins"
    :css-classes="cssClasses"
    :styles="styles"
    :width="width"
    :height="height"
  />
</template>

<script>
import { Pie } from 'vue-chartjs'
import { Chart as ChartJS, Title, Tooltip, Legend, ArcElement, CategoryScale, LinearScale } from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, ArcElement, CategoryScale, LinearScale)

export default {
  components: { Pie },
  props: {
    data: {
      type: Object,
      required: true
    },
    chartId: {
      type: String,
      default: 'bar-chart'
    },
    datasetIdKey: {
      type: String,
      default: 'label'
    },
    plugins: {
      type: Object,
      default: () => {}
    },
    styles: {
      type: Object,
      default: () => {}
    },
    width: {
      type: Number,
      default: 400
    },
    height: {
      type: Number,
      default: 400
    },
    cssClasses: {
      default: '',
      type: String
    },
  },
  data() {
    return {
      chartData: {
        labels: this.data.map((arr) => arr.name),
        datasets: [ {
          backgroundColor: ["#41B883", "#E46651", "#00D8FF", "#DD1B16", "#00D8FF", "#E46651", "#41B883"],  
          data: this.data.map((arr) => arr.val)
        }]
      },
      chartOptions: {
        responsive: true
      }
    }
  },
}
</script>
<script>
import { defineComponent } from 'vue'
import { Pie } from 'vue3-chart-v2'

export default defineComponent({
  name: 'ChartPie',
  extends: Pie,
  props: {
    chartDataPie: {
      type: Object,
      required: true
    },
    chartOptionsPie: {
      type: Object,
      required: false
    },
  },
  mounted () {
    this.renderChart(this.chartDataPie, this.chartOptionsPie)
  }
})
</script>
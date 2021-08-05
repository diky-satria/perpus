<script>
import { defineComponent } from 'vue'
import { Line } from 'vue3-chart-v2'

export default defineComponent({
  name: 'ChartLine',
  extends: Line,
  props: {
    chartDataLine: {
      type: Object,
      required: true
    },
    chartOptionsLine: {
      type: Object,
      required: false
    },
  },
  mounted () {
    this.renderChart(this.chartDataLine, this.chartOptionsLine)
  }
})
</script>
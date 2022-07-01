<template>
  <vue-highcharts
    class="container"
    type="chart"
    :options="chartOptions"
    :redrawOnUpdate="true"
    :oneToOneUpdate="false"
    :animateOnUpdate="true"
    @rendered="onRender"/>

</template>
<script>
  import VueHighcharts from 'vue3-highcharts';
  import { computed } from "vue";

  export default {
    name: 'SimpleChart',
    props: ['fiveDaystemperature','fiveDays'],

    components: {
      VueHighcharts,
    },

    setup(props) {
      const chartOptions = computed(() => ({
        chart: {
          type: 'column',
        },
        title: {
          text: '5 Days Forecast ',
        },
        xAxis: {
          categories: props.fiveDays,
        },
        yAxis: {
          title: {
            text: 'Derece',
          },
        },
        series: [{
          name: 'Hava Sıcaklığı',
          data: props.fiveDaystemperature,
        }],
      }));

      const onRender = () => {
        console.log('Chart rendered');
      };

      return {
        chartOptions,
        onRender,
      };
    },
  };
</script>
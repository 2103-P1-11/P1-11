<template>
  <div class="container">
    <line-chart
    v-if="loaded"
      :chartdata="chartdata"
      :options="options"/>
  </div>
</template>

<script>
const url = "https://hospitaldb1-11.herokuapp.com"
import LineChart from './LineChart.vue'
import axios from "axios";
export default {
  name: 'LineChartContainer',
  components: { LineChart },
  data: () => ({
    loaded: false,
    admissiondata: [],
    chartdata: {
      labels: [],
      datasets: [
        {
          label: 'Daily Covid Cases',
          backgroundColor: [
                'rgba(255, 99, 132, 0.2)',
            ],
            borderColor: [
                'rgba(255, 99, 132, 1)',
            ],
          data: []
        },
        
      ]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false
    }
  }),
  async mounted () {
    this.loaded = false
    await axios
        .get(url + "/db/admitweek")
        .then((response) => {
          this.admissiondata = response.data
          for (let i = 0; i < response.data.length; i ++){
            var dateObj = new Date(response.data[i]['admitted_week']);
            var month = dateObj.getUTCMonth() + 1; //months from 1-12
            var day = dateObj.getUTCDate();
            let monthtype = ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec']
            this.chartdata.labels.push(day.toString() + " " + monthtype[month-1])
            this.chartdata.datasets[0].data.push(response.data[i]['number_of_patients'])
          }
        })
        .catch((error) => {
          this.errorMessage = error.message;
          console.error("There was an error!", error);
        });

      this.loaded = true

  }
}
</script>
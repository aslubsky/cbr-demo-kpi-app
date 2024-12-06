<script setup>

import axios from 'axios';
import * as pym from 'pym.js';
import {ref, computed} from 'vue';

import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
} from 'chart.js'
import {Bar} from 'vue-chartjs'

ChartJS.register(CategoryScale, LinearScale, BarElement, Title, Tooltip, Legend)


if (localStorage.getItem('id_token')) {
  axios.defaults.headers.common['Authorization'] =
      'Bearer ' + localStorage.getItem('id_token');
}

const _generateUuid = () => {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
    const r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

let appId = 0;
let instanceId = _generateUuid();
if (location.pathname.indexOf('/uploads/applications/')) {
  const winUrl = new URL(window.location.href);
  appId = winUrl.searchParams.get('app_id');
  instanceId = `embed-app-${appId}`;
}


const pymChild = new pym.Child({
  id: instanceId
});
setInterval(() => {
  pymChild.sendHeight();
}, 250);

const user = ref()


user.value = {
  customInfo: {
    prevMonth2: [
      {x: '2024-10-01', y: 111},
      {x: '2024-10-02', y: 97},
      {x: '2024-10-03', y: 109},
      {x: '2024-10-04', y: 107},
      {x: '2024-10-05', y: 103},
      {x: '2024-10-06', y: 117},
      {x: '2024-10-07', y: 104},
      {x: '2024-10-08', y: 109},
      {x: '2024-10-09', y: 107},
      {x: '2024-10-10', y: 105},
      {x: '2024-10-11', y: 119},
      {x: '2024-10-12', y: 109},
      {x: '2024-10-13', y: 105},
      {x: '2024-10-14', y: 117},
      {x: '2024-10-15', y: 118},
      {x: '2024-10-16', y: 117},
      {x: '2024-10-17', y: 114},
      {x: '2024-10-18', y: 112},
      {x: '2024-10-19', y: 116},
      {x: '2024-10-20', y: 117},
      {x: '2024-10-21', y: 110},
      {x: '2024-10-22', y: 120},
      {x: '2024-10-23', y: 125},
      {x: '2024-10-24', y: 114},
      {x: '2024-10-25', y: 126},
      {x: '2024-10-26', y: 116},
      {x: '2024-10-27', y: 126},
      {x: '2024-10-28', y: 126},
      {x: '2024-10-29', y: 127},
      {x: '2024-10-30', y: 111}
    ],
    prevMonth1: [
      {x: '2024-11-01', y: 105},
      {x: '2024-11-02', y: 110},
      {x: '2024-11-03', y: 104},
      {x: '2024-11-04', y: 101},
      {x: '2024-11-05', y: 111},
      {x: '2024-11-06', y: 111},
      {x: '2024-11-07', y: 106},
      {x: '2024-11-08', y: 105},
      {x: '2024-11-09', y: 112},
      {x: '2024-11-10', y: 111},
      {x: '2024-11-11', y: 115},
      {x: '2024-11-12', y: 109},
      {x: '2024-11-13', y: 114},
      {x: '2024-11-14', y: 117},
      {x: '2024-11-15', y: 121},
      {x: '2024-11-16', y: 104},
      {x: '2024-11-17', y: 111},
      {x: '2024-11-18', y: 106},
      {x: '2024-11-19', y: 124},
      {x: '2024-11-20', y: 116},
      {x: '2024-11-21', y: 119},
      {x: '2024-11-22', y: 123},
      {x: '2024-11-23', y: 110},
      {x: '2024-11-24', y: 121},
      {x: '2024-11-25', y: 113},
      {x: '2024-11-26', y: 124},
      {x: '2024-11-27', y: 116},
      {x: '2024-11-28', y: 113},
      {x: '2024-11-29', y: 111},
      {x: '2024-11-30', y: 111}
    ],
    currentMonth: [
      {x: '2024-12-01', y: 120},
      {x: '2024-12-02', y: 122},
      {x: '2024-12-03', y: 118},
      {x: '2024-12-04', y: 110},
      {x: '2024-12-05', y: 105}
    ]
  }
};

const calculateGvg = arr => arr.reduceRight((a, v, i) => (a += v, i ? a : a / arr.length), 0);

const calculateAvgRound = arr => Math.round(calculateGvg(arr));

const calculateTrend = (data) => {
  if (data.length < 2) return 0;

  const lastValue = data[data.length - 1];
  const previousValue = data[data.length - 2];

  return lastValue - previousValue;
};

const forecastMetric = (data) => {
  const averageValue = calculateGvg(data);
  const trend = calculateTrend(data);
  return Math.round(averageValue + trend);
}

const allMetrics = [
  ...user.value.customInfo.prevMonth2.map(i => i.y),
  ...user.value.customInfo.prevMonth1.map(i => i.y),
  ...user.value.customInfo.currentMonth.map(i => i.y),
];
// console.log('allMetrics', allMetrics);

const avgDataset = [];

avgDataset.push({
  x: user.value.customInfo.prevMonth2[0].x.substring(0, 7),
  y: calculateAvgRound(user.value.customInfo.prevMonth2.map(i => i.y))
});

avgDataset.push({
  x: user.value.customInfo.prevMonth1[0].x.substring(0, 7),
  y: calculateAvgRound(user.value.customInfo.prevMonth1.map(i => i.y))
});

avgDataset.push({
  x: user.value.customInfo.currentMonth[0].x.substring(0, 7),
  y: forecastMetric(allMetrics)
});
// console.log('avgDataset', avgDataset);

const avgData = ref();
avgData.value = {
  labels: avgDataset.map(i => {
    return i.x;
  }),
  datasets: [{
    label: 'KPI',
    data: avgDataset
  }]
};

const period = ref('current');

const getCurrentData = computed(() => {
  let data = [];// user.value.customInfo.prevMonth2;
  switch (period.value) {
    case 'prev2':
      data = user.value.customInfo.prevMonth2;
      break;
    case 'prev1':
      data = user.value.customInfo.prevMonth1;
      break;
    case 'current':
      data = user.value.customInfo.currentMonth;
      break;
  }
  return {
    labels: data.map(i => {
      return i.x;
    }),
    datasets: [{
      label: 'KPI',
      data: data
    }]
  }
});

const options = ref()

options.value = {
  responsive: true
};

// axios
//     .get(`/api/v2/profile`)
//     .then(function (response) {
//       user.value = response.data.data;
//
//       setInterval(() => {
//         pymChild.sendHeight();
//       }, 250);
//     });
</script>

<template>
  <main>
    <div v-if="user && user.customInfo" class="container py-4">

      <div class="row mb-4 align-items-md-stretch">
        <div class="col-md-6">
          <div class="h-100 p-5 bg-body-tertiary border rounded-3">
            <h2>Поточне значення KPI</h2>
            <div class="kpi-current1">
              <div class="kpi-current">
                <div>{{ user.customInfo.currentMonth[user.customInfo.currentMonth.length - 1].y }}</div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-md-6">
          <div class="h-100 p-5 bg-body-tertiary border rounded-3">
            <h2>Тренд за останні три місяці</h2>
            <Bar :data="avgData" :options="options"/>
          </div>
        </div>
      </div>

      <div class="p-5  bg-body-tertiary rounded-3">
        <div class="container-fluid py-5">
          <div>
            Період:
            <div class="btn-group" role="group" aria-label="Basic example">
              <button type="button" @click="period = 'prev2'"
                      :class="{'btn-secondary': period === 'prev2', 'btn-light': period !== 'prev2'}" class="btn">
                10/2024
              </button>
              <button type="button" @click="period = 'prev1'"
                      :class="{'btn-secondary': period === 'prev1', 'btn-light': period !== 'prev1'}" class="btn">
                11/2024
              </button>
              <button type="button" @click="period = 'current'"
                      :class="{'btn-secondary': period === 'current', 'btn-light': period !== 'current'}" class="btn">
                12/2024
              </button>
            </div>
          </div>
          <Bar :data="getCurrentData" :options="options"/>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
.kpi-current1 {

  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px 0;
}

.kpi-current {
  padding: 10px;
  margin: 10px;
  border: 10px solid #c0c0c0;
  border-radius: 50%;
  font-size: 5rem;
  width: 200px;
  height: 200px;
  display: flex;
  justify-content: center;
  align-items: center;
}

main {
  display: block;
}
</style>

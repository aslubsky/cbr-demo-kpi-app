<script setup>

import axios from 'axios';
import * as pym from 'pym.js';
import { ref } from 'vue';

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

const user = ref('user')

axios
    .get(`/api/v2/profile`)
    .then(function (response) {
      user.value = response.data.data;

      setInterval(() => {
        pymChild.sendHeight();
      }, 250);
    });
</script>

<template>
  <main>
    <div v-if="user && user.customInfo" class="row row-cols-1 row-cols-md-3 mb-3 text-center">
      <div class="col">
        <div class="card mb-4 rounded-3 shadow-sm">
          <div class="card-header py-3">
            <h4 class="my-0 fw-normal">
              <svg width="128" height="128" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path fill-rule="evenodd" clip-rule="evenodd" d="M11.948 1.25H12.052C12.9505 1.24997 13.6997 1.24995 14.2945 1.32991C14.9223 1.41432 15.4891 1.59999 15.9445 2.05546C16.4 2.51093 16.5857 3.07773 16.6701 3.70552C16.7292 4.14512 16.7446 4.66909 16.7486 5.27533C17.3971 5.29614 17.9752 5.33406 18.489 5.40314C19.6614 5.56076 20.6104 5.89288 21.3588 6.64124C22.1071 7.38961 22.4392 8.33856 22.5969 9.51098C22.75 10.6502 22.75 12.1058 22.75 13.9436V14.0564C22.75 15.8942 22.75 17.3498 22.5969 18.489C22.4392 19.6614 22.1071 20.6104 21.3588 21.3588C20.6104 22.1071 19.6614 22.4392 18.489 22.5969C17.3498 22.75 15.8942 22.75 14.0564 22.75H9.94359C8.10583 22.75 6.65019 22.75 5.51098 22.5969C4.33856 22.4392 3.38961 22.1071 2.64124 21.3588C1.89288 20.6104 1.56076 19.6614 1.40314 18.489C1.24997 17.3498 1.24998 15.8942 1.25 14.0564V13.9436C1.24998 12.1058 1.24997 10.6502 1.40314 9.51098C1.56076 8.33856 1.89288 7.38961 2.64124 6.64124C3.38961 5.89288 4.33856 5.56076 5.51098 5.40314C6.02475 5.33406 6.60288 5.29614 7.2514 5.27533C7.2554 4.66909 7.27081 4.14512 7.32991 3.70552C7.41432 3.07773 7.59999 2.51093 8.05546 2.05546C8.51093 1.59999 9.07773 1.41432 9.70552 1.32991C10.3003 1.24995 11.0495 1.24997 11.948 1.25ZM8.7518 5.25178C9.12993 5.24999 9.52694 5.25 9.94358 5.25H14.0564C14.4731 5.25 14.8701 5.24999 15.2482 5.25178C15.244 4.68146 15.23 4.25125 15.1835 3.90539C15.1214 3.44393 15.0142 3.24644 14.8839 3.11612C14.7536 2.9858 14.5561 2.87858 14.0946 2.81654C13.6116 2.7516 12.964 2.75 12 2.75C11.036 2.75 10.3884 2.7516 9.90539 2.81654C9.44393 2.87858 9.24643 2.9858 9.11612 3.11612C8.9858 3.24644 8.87858 3.44393 8.81654 3.90539C8.77004 4.25125 8.75601 4.68146 8.7518 5.25178ZM5.71085 6.88976C4.70476 7.02503 4.12511 7.2787 3.7019 7.70191C3.27869 8.12511 3.02502 8.70476 2.88976 9.71085C2.75159 10.7385 2.75 12.0932 2.75 14C2.75 15.9068 2.75159 17.2615 2.88976 18.2892C3.02502 19.2952 3.27869 19.8749 3.7019 20.2981C4.12511 20.7213 4.70476 20.975 5.71085 21.1102C6.73851 21.2484 8.09318 21.25 10 21.25H14C15.9068 21.25 17.2615 21.2484 18.2892 21.1102C19.2952 20.975 19.8749 20.7213 20.2981 20.2981C20.7213 19.8749 20.975 19.2952 21.1102 18.2892C21.2484 17.2615 21.25 15.9068 21.25 14C21.25 12.0932 21.2484 10.7385 21.1102 9.71085C20.975 8.70476 20.7213 8.12511 20.2981 7.70191C19.8749 7.2787 19.2952 7.02503 18.2892 6.88976C17.2615 6.7516 15.9068 6.75 14 6.75H10C8.09318 6.75 6.73851 6.7516 5.71085 6.88976ZM12 9.25C12.4142 9.25 12.75 9.58579 12.75 10V10.0102C13.8388 10.2845 14.75 11.143 14.75 12.3333C14.75 12.7475 14.4142 13.0833 14 13.0833C13.5858 13.0833 13.25 12.7475 13.25 12.3333C13.25 11.9493 12.8242 11.4167 12 11.4167C11.1758 11.4167 10.75 11.9493 10.75 12.3333C10.75 12.7174 11.1758 13.25 12 13.25C13.3849 13.25 14.75 14.2098 14.75 15.6667C14.75 16.857 13.8388 17.7155 12.75 17.9898V18C12.75 18.4142 12.4142 18.75 12 18.75C11.5858 18.75 11.25 18.4142 11.25 18V17.9898C10.1612 17.7155 9.25 16.857 9.25 15.6667C9.25 15.2525 9.58579 14.9167 10 14.9167C10.4142 14.9167 10.75 15.2525 10.75 15.6667C10.75 16.0507 11.1758 16.5833 12 16.5833C12.8242 16.5833 13.25 16.0507 13.25 15.6667C13.25 15.2826 12.8242 14.75 12 14.75C10.6151 14.75 9.25 13.7903 9.25 12.3333C9.25 11.143 10.1612 10.2845 11.25 10.0102V10C11.25 9.58579 11.5858 9.25 12 9.25Z" fill="#1C274C"/>
              </svg>
            </h4>
          </div>
          <div class="card-body">
            <h1 class="card-title pricing-card-title">{{user.customInfo.kpi1}}<small class="text-body-secondary fw-light">грн</small></h1>
            <ul class="list-unstyled mt-3 mb-4">
              <li>Обсяг продажів за поточний місяць</li>
            </ul>
          </div>
        </div>
      </div>
      <div class="col">
        <div class="card mb-4 rounded-3 shadow-sm">
          <div class="card-header py-3">
            <h4 class="my-0 fw-normal">
              <svg width="128" height="128" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M16.755 2H7.24502C6.08614 2 5.50671 2 5.03939 2.16261C4.15322 2.47096 3.45748 3.18719 3.15795 4.09946C3 4.58055 3 5.17705 3 6.37006V20.3742C3 21.2324 3.985 21.6878 4.6081 21.1176C4.97417 20.7826 5.52583 20.7826 5.8919 21.1176L6.375 21.5597C7.01659 22.1468 7.98341 22.1468 8.625 21.5597C9.26659 20.9726 10.2334 20.9726 10.875 21.5597C11.5166 22.1468 12.4834 22.1468 13.125 21.5597C13.7666 20.9726 14.7334 20.9726 15.375 21.5597C16.0166 22.1468 16.9834 22.1468 17.625 21.5597L18.1081 21.1176C18.4742 20.7826 19.0258 20.7826 19.3919 21.1176C20.015 21.6878 21 21.2324 21 20.3742V6.37006C21 5.17705 21 4.58055 20.842 4.09946C20.5425 3.18719 19.8468 2.47096 18.9606 2.16261C18.4933 2 17.9139 2 16.755 2Z" stroke="#1C274C" stroke-width="1.5"/>
                <path d="M10.5 11L17 11" stroke="#1C274C" stroke-width="1.5" stroke-linecap="round"/>
                <path d="M7 11H7.5" stroke="#1C274C" stroke-width="1.5" stroke-linecap="round"/>
                <path d="M7 7.5H7.5" stroke="#1C274C" stroke-width="1.5" stroke-linecap="round"/>
                <path d="M7 14.5H7.5" stroke="#1C274C" stroke-width="1.5" stroke-linecap="round"/>
                <path d="M10.5 7.5H17" stroke="#1C274C" stroke-width="1.5" stroke-linecap="round"/>
                <path d="M10.5 14.5H17" stroke="#1C274C" stroke-width="1.5" stroke-linecap="round"/>
              </svg>
            </h4>
          </div>
          <div class="card-body">
            <h1 class="card-title pricing-card-title">{{user.customInfo.kpi2}}<small class="text-body-secondary fw-light">грн</small></h1>
            <ul class="list-unstyled mt-3 mb-4">
              <li>Середній чек</li>
            </ul>
          </div>
        </div>
      </div>
      <div class="col">
        <div class="card mb-4 rounded-3 shadow-sm">
          <div class="card-header py-3">
            <h4 class="my-0 fw-normal">
              <svg width="128" height="128" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M3.5 14C3.5 12.8838 3.71986 11.7785 4.14702 10.7472C4.57419 9.71592 5.2003 8.77889 5.98959 7.98959C6.77889 7.20029 7.71593 6.57419 8.74719 6.14702C9.77846 5.71986 10.8838 5.5 12 5.5C13.1162 5.5 14.2215 5.71986 15.2528 6.14703C16.2841 6.57419 17.2211 7.2003 18.0104 7.9896C18.7997 8.77889 19.4258 9.71593 19.853 10.7472C20.2801 11.7785 20.5 12.8838 20.5 14" stroke="#222222" stroke-linejoin="round"/>
                <path d="M9.5 15C9.5 14.6717 9.56466 14.3466 9.6903 14.0433C9.81594 13.74 10.0001 13.4644 10.2322 13.2322C10.4644 13.0001 10.74 12.8159 11.0433 12.6903C11.3466 12.5647 11.6717 12.5 12 12.5C12.3283 12.5 12.6534 12.5647 12.9567 12.6903C13.26 12.8159 13.5356 13.0001 13.7678 13.2322C13.9999 13.4644 14.1841 13.74 14.3097 14.0433C14.4353 14.3466 14.5 14.6717 14.5 15" stroke="#222222" stroke-linejoin="round"/>
                <path d="M13.5 12.5L15.5 9.5" stroke="#33363F" stroke-linecap="round" stroke-linejoin="round"/>
                <path d="M20.5 14V14.5C20.5 15.0523 20.0523 15.5 19.5 15.5H4.5C3.94772 15.5 3.5 15.0523 3.5 14.5V14" stroke="#222222" stroke-linecap="round" stroke-linejoin="round"/>
              </svg>
            </h4>
          </div>
          <div class="card-body">
            <h1 class="card-title pricing-card-title">{{user.customInfo.kpi3}}<small class="text-body-secondary fw-light">хв</small></h1>
            <ul class="list-unstyled mt-3 mb-4">
              <li>Швидкість обслуговування</li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>

main {
  display: block;
}
</style>

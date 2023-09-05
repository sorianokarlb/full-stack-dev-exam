<template>
  <div class="d-flex flex-column align-items-center justify-content-center m-5">
    <div class="row d-flex flex-column align-items-center justify-content-center">

      <div class="col-sm-12 col-md-12 searchHeader">
        <input type="text" class="form-control" placeholder="Enter keyword" v-model="searchQuery" @input="handleSearch">
      </div>

      <div class="col-sm-12 col-md-12 cardHolder p-5 mt-4 d-flex flex-column gap-3" >
        <div class="cardContainer">
          <template v-if="launches.length !== 0">
            <div class="container d-flex flex-row gap-3 mb-3" v-for="(i, index) in launches" :key="index">
              <div class="image">
                <img src="./assets/logo.svg" alt="" class="container-img">
              </div>
              <div class="details ">
                <span class="title">{{ i.flight_number }}: {{ i.name }} ({{ i.date_local }})</span><br>
                <span class="sub-details">Details: {{ i.details }}</span>
              </div>
            </div>
          </template>
          <div class="container" v-else>
            <span>No record found</span>
          </div>
        </div>
      </div>

      <div class="loader d-flex justify-content-center align-items" v-if="loading">
        <div class="spinner"></div>
      </div>
    
    </div>
  </div>
  </template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import moment from 'moment';

const launches = ref([]);
const loading = ref(false);
const noMoreData = ref(false);
let page = 1;

const searchQuery = ref('');
const baseURL = `https://api.spacexdata.com/v4/launches/?page=${page}`;

const fetchLaunches = async () => {
  if (loading.value || noMoreData.value ) return;
  loading.value = true;
  try {
    const response = await axios.get(baseURL);
    const newLaunches = response.data
    if (newLaunches.length === 0) {
      noMoreData.value = true;
    } else {
      launches.value.push(...newLaunches);
      page++;
    }
  } catch (error) {
    console.error("Error fetching data: ", error);
  } finally {
    loading.value = false;
  }
}


const handleSearch = async () => {
  const filteredLaunches = launches.value.filter((launch) =>
    launch.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
  launches.value = filteredLaunches;

  if (searchQuery.value === '') {
    fetchLaunches();
  }
};

onMounted(()=> {
  fetchLaunches();
  console.log(launches.value)
})
</script>

<style scoped>
.searchHeader{
  width: 50em;
  padding: 0;
}
.cardHolder{
  max-width: 50em;
  height: 30em;
  background-color: #fff;
  overflow-y: hidden;
  border-radius: 5px;
}


.cardContainer{
  max-height: 100%;
  overflow-y: auto;
}

.container-img{
  width: 5em;
  height: 5em;
}

.title{
  font-size: 18px;
  font-weight: bold;
  text-align: left;
}

.sub-details {
  font-size: 14px;
  font-weight: 400;
  text-align: left;
}

.spinner {
   width: 11.2px;
   height: 11.2px;
   border-radius: 11.2px;
   box-shadow: 28px 0px 0 0 rgba(71,75,255,0.2), 22.7px 16.5px 0 0 rgba(71,75,255,0.4), 8.68px 26.6px 0 0 rgba(71,75,255,0.6), -8.68px 26.6px 0 0 rgba(71,75,255,0.8), -22.7px 16.5px 0 0 #474bff;
   animation: spinner-b87k6z 1s infinite linear;
}

@keyframes spinner-b87k6z {
   to {
      transform: rotate(360deg);
   }
}
</style>
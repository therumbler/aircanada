<template>
<div>
  <h2>Index</h2>
  <form class="form" @submit.prevent="performSearch">
      <input type="text" v-model="flightNumber" placeholder="Search" />
      <select v-model="date" id="date" name="date">
        <option v-for="date in dates" :value="date" :key="date">{{ date }}</option>
      </select>

      <input type="submit" value="Search" />
    </form>
  </div>
</template>
<script setup>
const useFlightNumber = () => useState("flightNumber");
const useDates = () => useState("dates");

const flightNumber = useFlightNumber();
const dates = useDates();

dates.value = createDates();

function pad2(n) {
  return (n < 10 ? '0' : '') + n;
}

function dateToMMDDYYYY(date) {
  var month = pad2(date.getMonth()+1);//months (0-11)
  var day = pad2(date.getDate());//day (1-31)
  var year = date.getFullYear();

  return `${month}-${day}-${year}`;
}

function createDates(){
    const dates = [];
    const today = new Date();
    const year = today.getFullYear();
    const month = today.getMonth();
    const day = today.getDate();
    for(let i = 0; i < 7; i++){
        const date = new Date(year, month, day + i);
        dates.push(dateToMMDDYYYY(date));
    }
    return dates;
}
async function fetchFlightStatus(flightNumber, date){
    const parser = new DOMParser();
    let url=`https://alpha.irumble.com/aircanadaflightstatus/?forceTimetable=true&flightNumber=${flightNumber}&carrierCode=AC&date=${date}&app_key=AE919FDCC80311DF9BABC975DFD72085&cache=26928`
    console.log('url', url);
    // return false
    const resp = await fetch(url);
    const xmlStr = await resp.text();
    const doc = parser.parseFromString(xmlStr, "application/xml");
    return doc;
}

async function performSearch(){
    console.log('performSearch');
    const doc = await fetchFlightStatus(flightNumber.value, dates.value);
}
</script>

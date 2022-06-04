<template>
<div>
  <h2>Index</h2>
  <form class="form" @submit.prevent="performSearch">
      <input type="text" v-model="flightNumber" placeholder="Search"/>
      <select v-model="date" id="date" name="date">
        <option v-for="(date, index) in dates" :value="date" :key="date" v-bind:selected="index === 0">{{ date }}</option>
      </select>

      <input type="submit" value="Search" />
    </form>
    <Flight v-if="flight" :flight="flight" />
  </div>
</template>
<script setup>
const useFlightNumber = () => useState("flightNumber");
const useDates = () => useState("dates");
const useDate = () => useState("date");
const useFlight = () => useState("flight");

const flightNumber = useFlightNumber();
const dates = useDates();
const date = useDate();
const flight = useFlight();

dates.value = createDates();
flightNumber.value = "189";

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
    const flightParsed = docToFlight(doc);
    console.log('flightParsed', flightParsed);
    flight.value = flightParsed;
}
function docToFlight(doc){
  let flight = doc.querySelector("Flights");
  let segment = flight.querySelector("Segments")
  return {
    flightNumber: getFlightNumberFromSegment(segment),
    status: getStatusFromSegment(segment),
    departure: getDepartureTimesFromSegment(segment),
    arrival: getArrivalTimesFromSegment(segment)
  }
}

function getStatusFromSegment(segment){
  return segment.querySelector("OverallStatus").childNodes[0].nodeValue;
}
 function dateTimeToTime(dateString){
    return dateString.slice(11,16);
}

function getDepartureTimesFromSegment(segment){
 let departureStationInfo = segment.querySelector("DepartureStationInfo");
 let departureScheduledTime = departureStationInfo.querySelector("ScheduledTime");
 let departureEstimatedTime = departureStationInfo.querySelector("EstimatedTime");
 return {
   scheduledTime: dateTimeToTime(departureScheduledTime.childNodes[0].nodeValue),
   estimatedTime: dateTimeToTime(departureEstimatedTime.childNodes[0].nodeValue),
 }
}

function getArrivalTimesFromSegment(segment){
  let arrivalStationInfo = segment.querySelector("ArrivalStationInfo");
  let arrivalScheduledTime = arrivalStationInfo.querySelector("ScheduledTime");
  let arrivalEstimatedTime = arrivalStationInfo.querySelector("EstimatedTime");
  return {
    scheduledTime: dateTimeToTime(arrivalScheduledTime.childNodes[0].nodeValue),
    estimatedTime: dateTimeToTime(arrivalEstimatedTime.childNodes[0].nodeValue),
  }
}

function getFlightNumberFromSegment(segment){
  return segment.querySelector("FlightNumber").childNodes[0].nodeValue;
}
</script>

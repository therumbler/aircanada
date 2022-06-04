<template>
<h2>Flight Details: AC{{flight.flightNumber}}</h2>
<h3>Status: {{flight.status}}</h3>
<div>{{flight}}</div>
<div>
    <h3>Departure</h3>
    <strong>Scheduled:</strong> {{flight.departure.scheduledTime}}
    <strong>Estimated:</strong>{{flight.departure.estimatedTime}}
</div>
<div>
    <h3>Arrival</h3>
    <strong>Scheduled:</strong> {{flight.arrival.scheduledTime}}
    <strong>Estimated:</strong>{{flight.arrival.estimatedTime}}
</div>
</template>
<script>
export default {
    name: "Flight",
    props: {
        flight: {
            type: Object,
            required: true
        }
    }
}
</script>

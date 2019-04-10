<template>
  <div id="app">
    <Ticket v-for="ticket in tickets" v-bind:key="ticket.id" :ticket="ticket"/>
  </div>
</template>

<script>
import axios from 'axios'; // We use Axios to fetch our data
import Ticket from './components/Ticket.vue';

export default {
  name: 'app',
  data() {
    return {
        loading: true,
        tickets: [],
        customers: [],
        pagination: {
            totalPages: 1,
            itemsPerPage: 25,
            currentPage: 1,
        },
        errors: []
    };
  },
  components: {
    Ticket
  },
  mounted() {
        // Use Axios to make an ajax request to our local data.
        axios.get('./data/data.json')
        .then(response => {
            // Populate tickets and customers arrays with the response
            this.customers = response.data.customers;
            this.tickets = response.data.tickets;
        })
        .catch(e => {
            // Fill our errors array with any errors
            this.errors.push(e)
        })
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

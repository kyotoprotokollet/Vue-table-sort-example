<template>
  <div id="app">
      <div class="tickets">
            <Ticket v-for="ticket in tickets" :key="ticket.id" :ticket="ticket"/>
      </div>
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
        }
    },
    components: {
        Ticket
    },
    mounted() {
        // Use Axios to make an ajax request to our local data.
        axios.get('./data/data.json')
        .then(response => {
            // Populate the tickets array with the response
            this.tickets = response.data.tickets;
        })
        .catch(e => {
            // Fill our errors array with any errors
            this.errors.push(e)
        })
    }
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css?family=Lato:300,400,700,900');
@import "./src/assets/scss/_globals.scss";

body {
    background-color: $color-egg;
}

.tickets {
    padding: 2.5vw;
    max-width: 1500px;
    margin: 0 auto;
    font-size: 15px;
}

#app {
  font-family: 'Lato', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: $color-text;
  margin-top: 60px;
}
</style>

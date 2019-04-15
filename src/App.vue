<template>
    <div id="app" class="tickets-wrapper">
        <table class="tickets-table">
            <TicketsSorter :tickets="tickets" @sortTickets="updateTickets"/>
            <tr is="Ticket" v-for="ticket in filteredTickets" :key="ticket.id" :ticket="ticket"/>
        </table>
    </div>
</template>

<script>
import axios from 'axios'; // Use Axios to fetch our data. https://github.com/axios/axios

import Ticket from './components/Ticket.vue'; // Ticket entry component
import TicketsSorter from './components/TicketsSorter.vue'; // Ticket header/sorting component

export default {
    name: 'app',
    data() {
        return {
            tickets: [],
            filteredTickets: [],
            errors: []
        }
    },
    components: {
        Ticket,
        TicketsSorter
    },
    methods: {
        // When the sorting component emits the sortTicket event (passing the sorted tickets array), update filteredTickets in with the data
        updateTickets (updatedTickets) {
            this.filteredTickets = updatedTickets
        }
    },
    mounted() {
        // When mounted, use Axios to make an ajax request to our local data.
        axios.get('./data/data.json')
        .then(response => {
            // Populate the tickets and filteredTickets array with the inial data
            this.tickets = response.data.tickets
            this.filteredTickets = response.data.tickets
        })
        .catch(e => {
            // Fill our errors array with any errors
            this.errors.push(e)
        })
    }
}
</script>

<style lang="scss">
// Import font and some settings and global styles
@import url('https://fonts.googleapis.com/css?family=Lato:300,400,700,900');
@import "./src/assets/scss/_globals.scss";

body {
    background-color: rgb(224, 236, 235);
    font-family: 'Lato', sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: $color-text;
    padding: 0 2.5vw;
    margin-top: 10px;

    @include respond-above("large") {
        margin-top: 60px;
    }
}

.tickets-wrapper {
    max-width: 1500px;
    margin: 0 auto;
    position: relative;
}

.tickets-table {
    display: block;
    font-size: 14px;
    
    @include respond-above("large") {
        display: table;
        table-layout: fixed;
        width: 100%;
    }

    tr {
        display: block;
        background-color: white;
        margin-bottom: 10px;
        padding: 10px;

        @include respond-above("large") {
            display: table-row;
            margin-bottom: 0;
            padding: 0;
        }
    }
}
</style>
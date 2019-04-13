<template>
    <div id="app">
        <table class="tickets">
            <thead class="tickets__header">
                <tr>
                    <th>
                        Ärendenummer
                    </th>
                    <th>
                        Namn
                    </th>
                    <th>
                        Personnummer
                    </th>
                    <th class="text-right">
                        Ansökt belopp
                    </th>
                    <th class="text-right">
                        Beviljat belopp
                    </th>
                    <th class="text-right">
                        Skapat
                    </th>
                    <th class="text-right">
                        Senast ändrat
                    </th>
                    <th class="text-center">
                        Status
                    </th>
                    <th></th>
                </tr>
            </thead>
            <tr is="ticket" v-for="ticket in tickets" :key="ticket.id" :ticket="ticket"/>
        </table>
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
    display: block;
    padding: 15px;
    max-width: 1500px;
    width: 100%;
    margin: 0 auto;
    font-size: 14px;

    @include respond-above("large") {
        display: table;
        padding: 0;
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

.tickets__header {
    display: none;

    @include respond-above("large") {
        display: table-header-group;
    }

    th {
        position: sticky;
        top: 0;
        background-color: white;
        padding: 14px;
        font-size: 12px;
        color: grey;
        font-weight: 600;
        text-transform: uppercase;
        letter-spacing: .5px;
        border-bottom: 1px solid #ebebeb;
        box-shadow: 0 5px 5px rgba(0,0,0,.02);

        &.text-right {
            text-align: right;
        }
        
        &.text-center {
            text-align: center;
        }
    }
}

#app {
  font-family: 'Lato', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: $color-text;
  margin-top: 60px;
}
</style>

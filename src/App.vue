<template>
    <div id="app">
        <div class="ticket-component">
            <div class="ticket-settings">
                <ul class="ticket-pagination">
                    <li class="pagination-button" v-for="page in pagination.totalPages">
                        <a class="button" @click="pagination.currentPage = page">{{ page }}</a>
                    </li>
                </ul>
            </div>
            <table class="tickets">
                <thead class="tickets__header">
                    <tr>
                        <th @click="sort('id')">
                            Ärendenummer
                        </th>
                        <th @click="sort('name')">
                            Namn
                        </th>
                        <th>
                            Personnummer
                        </th>
                        <th class="text-right" @click="sort('requested_amount')">
                            Ansökt belopp
                        </th>
                        <th class="text-right" @click="sort('granted_amount')">
                            Beviljat belopp
                        </th>
                        <th class="text-right" @click="sort('created_at')">
                            Skapat
                        </th>
                        <th class="text-right" @click="sort('updated_at')">
                            Senast ändrat
                        </th>
                        <th class="text-center" @click="sort('status')">
                            Status
                        </th>
                        <th class="button-column"></th>
                    </tr>
                </thead>
                <tr is="ticket" v-for="ticket in ticketsFinal" :key="ticket.id" :ticket="ticket"/>
            </table>
        </div>
    </div>
</template>

<script>
import axios from 'axios'; // We use Axios to fetch our data
import _ from 'lodash'; // We use lodash to sort some arrays
import Ticket from './components/Ticket.vue'; // Our ticket component

export default {
    name: 'app',
    data() {
        return {
            tickets: [],
            sortColumn: 'name',
            sortColumnDirection: 'asc', 
            pagination: {
                totalPages: 1,
                itemsPerPage: 8,
                currentPage: 1,
            },
            errors: []
        }
    },
    components: {
        Ticket
    },
    methods: {
        // Return a sorted column
        sort(columnName) {
            //If this column is the active sorting column, reverse the sort direction
            if ( columnName == this.sortColumn ) {
                this.sortColumnDirection = this.sortColumnDirection == 'asc' ? 'desc' : 'asc'
            }
            // Set the current sorting column to the column that the user clicked on
            this.sortColumn = columnName
        },

        // Return a paginated array, using our pagination settings
        paginateTickets(tickets, currentPage, itemsPerPage) {
            --currentPage;
            this.pagination.totalPages = Math.ceil(this.tickets.length / itemsPerPage);
            let paginatedTickets = tickets.slice(currentPage * itemsPerPage, (currentPage + 1) * itemsPerPage);
            return paginatedTickets;
        }
    },
    computed: {
        // First we sort our tickets
        ticketsSorted() {
            return this.tickets.sort((a,b) => {
                let modifier = 1
                    if ( this.sortColumnDirection == 'desc' ) modifier = -1
                    if (a[this.sortColumn] < b[this.sortColumn] ) return -1 * modifier
                    if (a[this.sortColumn] > b[this.sortColumn] ) return 1 * modifier
                return 0
            });
        },
        // The final data that we display. Paginate the sorted tickets.
        ticketsFinal() {
            return this.paginateTickets(this.ticketsSorted, this.pagination.currentPage, this.pagination.itemsPerPage);
        },
    },
    mounted() {
        // When mounted, use Axios to make an ajax request to our local data.
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
// Import font and some settings and global styles
@import url('https://fonts.googleapis.com/css?family=Lato:300,400,700,900');
@import "./src/assets/scss/_globals.scss";

body {
    background-color: $color-egg;
}

.button-column {
    width: 100px;
}

.ticket-component {
    max-width: 1500px;
    margin: 0 auto;
}

.ticket-settings {
    display: flex;
    background-color: white;
    padding: 15px;
    border-radius: 5px;
    margin-bottom: 15px;
}

.ticket-pagination {
    display: flex;
}

.tickets {
    display: block;
    width: 100%;
    font-size: 14px;

    @include respond-above("large") {
        display: table;
        table-layout: fixed;
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
  padding: 0 2.5vw;
}
</style>

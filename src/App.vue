<template>
    <div id="app">
        <div class="ticket-component">
            <table class="tickets">
                <thead class="tickets__header">
                    <tr>
                        <th :class="{ active: sortColumn == 'id' }" @click="setSortSettings('id')">
                            <span class="column-label">Ärende</span>
                        </th>
                        <th :class="{ active: sortColumn == 'customer.name' }" @click="setSortSettings('customer.name')">
                            <span class="column-label">Namn</span>
                        </th>
                        <th :class="{ active: sortColumn == 'customer.personal_identity_number' }" @click="setSortSettings('customer.personal_identity_number')">
                            Person nr.
                        </th>
                        <th :class="{ active: sortColumn == 'requested_amount' }" class="text-right" @click="setSortSettings('requested_amount')">
                            Ansökt belopp
                        </th>
                        <th :class="{ active: sortColumn == 'granted_amount' }" class="text-right" @click="setSortSettings('granted_amount')">
                            Beviljat belopp
                        </th>
                        <th :class="{ active: sortColumn == 'created_at' }" class="text-right" @click="setSortSettings('created_at')">
                            Skapat
                        </th>
                        <th :class="{ active: sortColumn == 'updated_at' }" class="text-right" @click="setSortSettings('updated_at')">
                            Senast ändrat
                        </th>
                        <th :class="{ active: sortColumn == 'status' }"  class="text-center" @click="setSortSettings('status')">
                            Status
                        </th>
                        <th class="button-column">
                            <ul class="ticket-pagination">
                                <li class="pagination-button" v-for="page in pagination.totalPages">
                                    <a class="button" @click="pagination.currentPage = page">{{ page }}</a>
                                </li>
                            </ul>
                        </th>
                    </tr>
                </thead>
                <tr is="ticket" v-for="ticket in ticketsFinal" :key="ticket.id" :ticket="ticket"/>
            </table>
        </div>
    </div>
</template>

<script>
import axios from 'axios'; // We use Axios to fetch our data
import _ from 'lodash'; // We use lodash for easy array sorting
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
        // When clicking on a column name, set the sorting setting
        setSortSettings(columnName) {
            //If this column is the active sorting column, reverse the sort direction
            if ( columnName == this.sortColumn ) {
                this.sortColumnDirection = this.sortColumnDirection == 'asc' ? 'desc' : 'asc'
            }
            // Set the current sorting column to the column that the user clicked on
            this.sortColumn = columnName
            //console.log(this.sortColumn, this.sortColumnDirection);
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
        // First we sort our tickets. This function reacts to our column sorting settings.
        ticketsSorted() {
            return _.orderBy(this.tickets, this.sortColumn, this.sortColumnDirection); 
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
            cursor: pointer;

            &.active {
               border-bottom: 1px solid teal;

            }

            &.text-right {
                text-align: right;
            }
            
            &.text-center {
                text-align: center;
            }
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

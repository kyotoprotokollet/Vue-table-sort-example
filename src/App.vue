<template>
    <div id="app">
        <div class="ticket-component">
            <table class="tickets">
                <thead class="tickets__header">
                    <tr>
                        <th class="is-sortable" :class="{ active: sortColumn == 'id' }" v-on:keyup.enter="setSortSettings('id')" @click="setSortSettings('id')" tabindex="0" >
                            <span class="column-label">Ärende</span>
                        </th>
                        <th class="is-sortable" :class="{ active: sortColumn == 'customer.name' }" v-on:keyup.enter="setSortSettings('customer.name')" @click="setSortSettings('customer.name')" tabindex="0" >
                            <span class="column-label">Namn</span>
                        </th>
                        <th class="is-sortable" :class="{ active: sortColumn == 'customer.personal_identity_number' }" v-on:keyup.enter="setSortSettings('customer.personal_identity_number')" @click="setSortSettings('customer.personal_identity_number')" tabindex="0" >
                            Person nr.
                        </th>
                        <th class="is-sortable text-right" :class="{ active: sortColumn == 'requested_amount' }" v-on:keyup.enter="setSortSettings('requested_amount')" @click="setSortSettings('requested_amount')" tabindex="0" >
                            Ansökt belopp
                        </th>
                        <th class="is-sortable text-right" :class="{ active: sortColumn == 'granted_amount' }" v-on:keyup.enter="setSortSettings('granted_amount')" @click="setSortSettings('granted_amount')" tabindex="0" >
                            Beviljat belopp
                        </th>
                        <th class="is-sortable text-right" :class="{ active: sortColumn == 'created_at' }" v-on:keyup.enter="setSortSettings('created_at')" @click="setSortSettings('created_at')" tabindex="0" >
                            Skapat
                        </th>
                        <th class="is-sortable text-right" :class="{ active: sortColumn == 'updated_at' }" v-on:keyup.enter="setSortSettings('updated_at')" @click="setSortSettings('updated_at')" tabindex="0" >
                            Senast ändrat
                        </th>
                        <th class="is-sortable text-center" :class="{ active: sortColumn == 'status' }" v-on:keyup.enter="setSortSettings('status')" @click="setSortSettings('status')" tabindex="0" >
                            Status
                        </th>
                        <th class="button-column">
                            <div class="ticket-pagination">
                                <button class="ticket-pagination__button ticket-pagination__button--backward" :disabled="pagination.currentPage == 1" @click="pagination.currentPage --">
                                    <svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" viewBox="0 0 90 90" xml:space="preserve">
                                        <path fill="00A081" class="st0" d="M45,0C20.2,0,0,20.2,0,45s20.2,45,45,45s45-20.2,45-45S69.8,0,45,0z M39.2,66.2l-6.4-6.4L47.6,45L32.8,30.2l6.4-6.4L60.4,45L39.2,66.2z"/>
                                    </svg>
                                </button>
                                <button class="ticket-pagination__button ticket-pagination__button--forward" :disabled="pagination.currentPage == pagination.totalPages" @click="pagination.currentPage ++">
                                    <svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" viewBox="0 0 90 90" xml:space="preserve">
                                        <path fill="00A081" class="st0" d="M45,0C20.2,0,0,20.2,0,45s20.2,45,45,45s45-20.2,45-45S69.8,0,45,0z M39.2,66.2l-6.4-6.4L47.6,45L32.8,30.2l6.4-6.4L60.4,45L39.2,66.2z"/>
                                    </svg>
                                </button>
                            </div>
                        </th>
                    </tr>
                </thead>
                <tr is="Ticket" v-for="ticket in ticketsFinal" :key="ticket.id" :ticket="ticket"/>
            </table>
        </div>
    </div>
</template>

<script>
import axios from 'axios'; // Use Axios to fetch our data. https://github.com/axios/axios
import _ from 'lodash'; // Use lodash for easy array sorting. https://lodash.com/
import Ticket from './components/Ticket.vue'; // Ticket component

export default {
    name: 'app',
    data() {
        return {
            tickets: [],
            sortColumn: 'status',
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
            this.tickets = response.data.tickets
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
}

.button-column {
    width: 100px;
}

.ticket-component {
    max-width: 1500px;
    margin: 0 auto;
    position: relative;
}

.tickets {
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

.tickets__header {
    display: block;

    @include respond-below("large") {
        tr {
            display: grid;
            grid-template-columns: repeat( auto-fit, minmax(100px, 1fr) );
        }
    }

    @include respond-above("large") {
        display: table-header-group;
    }
}

th {
    padding: 1rem;
    font-size: 11px;
    color: grey;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: .5px;

    @include respond-above("large") {
        position: sticky;
        top: 40px;
        background-color: white;
        border-bottom: 1px solid #ebebeb;
        box-shadow: 0 5px 5px rgba(0,0,0,.02);
    }

    &.is-sortable {
        cursor: pointer;
    }

    @include respond-above("large") {            
        &.active {
            border-bottom: 1px solid teal;
            &.asc:after {
                content: 'asc';
                display: inline-block;
                margin-left: 5px;
            }
        }
        &.text-right {
            text-align: right;
        }
        
        &.text-center {
            text-align: center;
        }
    }

    @include respond-above("huge") {            
        font-size: 12px;
    }
}

.ticket-pagination {
    display: flex;
    justify-content: center;
}

.ticket-pagination__button {
    width: 24px;
    height: 24px;

    svg {
        fill: $color-link;
        transition: .25s fill ease-out;

        &:hover {
            fill: $color-link--hover;
        }
    }

    &:disabled {
        cursor: not-allowed;
        svg {
            fill: grey;
        }
    }

    & + .ticket-pagination__button {
        margin-left: 5px;
    }

    &.ticket-pagination__button--backward {
        transform: rotate(180deg);
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

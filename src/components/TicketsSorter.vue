<template>
    <thead class="tickets__header">
        <tr>
            <th class="is-sortable" :class="{ active: sortColumn == 'id', asc: sortColumnDirection == 'asc', desc: sortColumnDirection == 'desc' }" v-on:keyup.enter="setSortSettings('id')" @click="setSortSettings('id')" tabindex="0" >
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
</template>

<script>
import _ from 'lodash'; // Use lodash for easy array sorting. https://lodash.com/

export default {
    name: 'TicketsSorter',
    props: {
        tickets: {
            type: Array,
            required: true
        }
    },
    data() {
        return {
            sortColumn: 'status',
            sortColumnDirection: 'asc', 
            pagination: {
                totalPages: 1,
                itemsPerPage: 8,
                currentPage: 1,
            }
        }
    },
    methods: {
        // When clicking on a column name, set the sorting settings
        setSortSettings(columnName) {
            //If this column is the active sorting column, reverse the sort direction
            if ( columnName == this.sortColumn ) {
                this.sortColumnDirection = this.sortColumnDirection == 'asc' ? 'desc' : 'asc'
            }
            // Set the current sorting column to the column that the user clicked on
            this.sortColumn = columnName
        },

        // Return a paginated array, using our pagination settings
        paginateTickets(tickets, currentPage, itemsPerPage) {
            --currentPage
            this.pagination.totalPages = Math.ceil(this.tickets.length / itemsPerPage)
            let paginatedTickets = tickets.slice(currentPage * itemsPerPage, (currentPage + 1) * itemsPerPage)
            return paginatedTickets
        }
    },
    computed: {
        // Sort the tickets. This function reacts to changes in our column sorting settings.
        ticketsSorted() {
            return _.orderBy(this.tickets, this.sortColumn, this.sortColumnDirection); 
        },
        // Paginate the tickets. This function takes the sorted tickets and paginates them
        ticketsFinal() {
            return this.paginateTickets(this.ticketsSorted, this.pagination.currentPage, this.pagination.itemsPerPage)
        },
    },
    watch: {
        // When the computed property ticketsFinal updates, emit an event with the final array. 
        // The parent App.vue will render the Ticket component based on this data
        ticketsFinal(ticketsFinal) {
            this.$emit('sortTickets', ticketsFinal);
        }
    },
  }
</script>

<style lang="scss" scoped>
@import "./src/assets/scss/_globals.scss";

.tickets__header {
    @include respond-below("large") {
        display: block;
    }

    tr {
        @include respond-below("large") {
            display: grid;
            grid-template-columns: repeat( auto-fit, 20% );
        }
    }
}

th {
    padding: .5rem;
    font-size: 11px;
    color: grey;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: .5px;

    &.is-sortable {
        cursor: pointer;
    }

    @include respond-above("large") {
        font-size: 12px;
        padding: 1rem;    
        position: sticky;
        top: 40px;
        border-bottom: 1px solid $color-border;
        box-shadow: 0 5px 5px rgba(0,0,0,.02);

        &.active {
            border-bottom: 1px solid teal;
        }

        &.text-right {
            text-align: right;
        }
        
        &.text-center {
            text-align: center;
        }

        &.button-column {
            width: 100px;
        }
    }
}

.ticket-pagination {
    display: flex;
    justify-content: center;
}



.ticket-pagination__button {
    width: 24px;
    height: 24px;

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


</style>

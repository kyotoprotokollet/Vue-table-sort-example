<template>
  <tr class="ticket">
    <td class="ticket-section ticket-section--id" data-label="Ärendenummer">
        <!-- I imagine this link leading to another view with details about this ticket -->
        # <a href="#">{{ ticket.id }}</a>
    </td>
    <td class="ticket-section" data-label="Kund">
        <a href="#">{{ ticket.customer.name }}</a>
    </td>
    <td class="ticket-section" data-label="Personnummer">
        <!-- I imagine this link leading to another view with details about this customer -->
        <a href="#">{{ ticket.customer.personal_identity_number }}</a>
    </td>
    <td class="ticket-section align-right" data-label="Ansökt belopp">
        {{ formatNumber(ticket.requested_amount) }}
    </td>
    <td class="ticket-section align-right" data-label="Beviljat belopp">
        {{ formatNumber(ticket.granted_amount) }}
    </td>
    <td class="ticket-section align-right" data-label="Skapat">
        <span class="ticket-date">
            {{ formatDate(ticket.created_at) }}
        </span>
    </td>
    <td class="ticket-section align-right" data-label="Senaste ändrat">
        <span class="ticket-date">
            {{ formatDate(ticket.updated_at) }}
        </span>
    </td>
    <td class="ticket-section align-center" data-label="Status">
        <span class="badge" :class="ticket.status">
            {{ ticket.status }}
        </span>
    </td>
    <td class="ticket-section">
        <button class="button--edit">Redigera</button>
    </td>
  </tr>
</template>

<script>
// Use the moment library to format the time
// https://momentjs.com/
import moment from 'moment';

export default {
    name: 'Ticket',
    props: {
        ticket: {
            type: Object,
            required: true
        }
    },
    data() {
        return {
            // Spread props to data
            ...this.ticket
        }
    },
    methods: {
        // Format the date
        formatDate(date) {
            return moment(date).format('YYYY-MM-DD')
        },
        // Format the number to a more human readable version, omit decimals
        formatNumber(number) {
            return number.toLocaleString('sv-SE', { 
                style: 'currency', 
                currency: 'SEK' ,
                minimumFractionDigits: 0,
                maximumFractionDigits: 0,
            })
        }
    }
  }
</script>



<style lang="scss" scoped>
@import "./src/assets/scss/_globals.scss";

.ticket {
    @include respond-above("large") {
            border-bottom: 1px solid #ebebeb;
            &:hover {
                .ticket-section {
                    background-color: #f8f8f8;
                }
            }
    }
}

.ticket-section {
    padding: 15px;
    background-color: white;

    // On smaller viewports, lets display our data as a list
    @include respond-below("large") {
        display: flex;
        padding: 10px;
        border-bottom: 1px solid #ebebeb;
        font-size: 80%;

        &[data-label] {
            &:before {
                content: attr(data-label);
                width: 50%;
            }
        }
     }
    
    @include respond-above("large") {
        &.align-right {
            text-align: right;
        }

        &.align-center {
            text-align: center;
        }
        & + .ticket-section {
            border-left: 1px solid #ebebeb;
        }
    }

}

.badge {
    padding: 5px 10px;
    background-color: rgb(49, 49, 49);
    color: white;
    border-radius: 10px;

    &.active {
        background-color: #2e85cc;
    }

    &.completed {
        background-color: #2ecc71;
    }
}

.button--edit {
    padding: 5px;
}


</style>

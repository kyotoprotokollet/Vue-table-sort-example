<template>
  <div class="ticket">
    <div class="ticket-section ticket-section--id" data-label="Ärendenummer">
        <!-- I imagine this link leading to another view with details about this ticket -->
        # <a href="#">{{ ticket.id }}</a>
    </div>
    <div class="ticket-section" data-label="Kund">
        {{ ticket.customer.name }}
    </div>
    <div class="ticket-section align-right" data-label="Ansökt belopp">
        {{ formatNumber(ticket.requested_amount) }}
    </div>
    <div class="ticket-section align-right" data-label="Beviljat belopp">
        {{ formatNumber(ticket.granted_amount) }}
    </div>
    <div class="ticket-section align-right" data-label="Påbörjat">
        <span class="ticket-date">
            {{ formatDate(ticket.created_at) }}
        </span>
    </div>
    <div class="ticket-section align-right" data-label="Senaste handling">
        <span class="ticket-date">
            {{ formatDate(ticket.updated_at) }}
        </span>
    </div>
    <div class="ticket-section align-center" data-label="Status">
        <span class="badge" :class="ticket.status">
            {{ ticket.status }}
        </span>
    </div>
    <div class="ticket-section">
        <button class="button--edit">Redigera</button>
    </div>
  </div>
</template>

<script>
// Use the moment library to format the time
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
    background-color: white;
    padding: 20px;
    
    @include respond-above("medium") {
        display: grid;
        grid-template-columns: 2fr repeat(6, 3fr) max-content;
    }

    & + .ticket {
        margin-top: 10px;
    }
}

.ticket-section {
    padding: 7px;
    border-bottom: 1px solid #ebebeb;
    font-size: 80%;
    display: flex;
    font-variant: common-ligatures tabular-nums;

    &[data-label] {
        &:before {
            content: attr(data-label);
            width: 50%;
        }
    }

    @include respond-above("medium") {
        display: block;
        padding: 0 10px;
        border-bottom: none;
        position: relative;
        font-size: 100%;
        align-self: center;

        &:after {
            content: '';
            display: block;
            position: absolute;
            top: 0;
            bottom: 0;
            left: 0;
            width: 2px;
            background-color: #ebebeb;
        }

        &[data-label] {
            &:before {
                content: '';
            }
        }

        &.align-right {
            text-align: right;
        }

        &.align-center {
            text-align: center;
        }
    }
}

.ticket-section--id {
    @include respond-above("medium") {
        font-size: 90%;
    }
}

.badge {
    padding: 7px 10px;
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

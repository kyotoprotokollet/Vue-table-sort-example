<template>
  <tr class="ticket-row">
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
    <td class="ticket-section ticket-section--button align-center">
        <button type="button" class="button--edit" @click="activeSubmenu = !activeSubmenu">
            <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" x="0px" y="0px" viewBox="0 0 24 30" xml:space="preserve">
                <path d="M19.607,18.746c0,0.881-0.716,1.624-1.597,1.624H5.231c-0.881,0-1.597-0.743-1.597-1.624V5.967  c0-0.881,0.716-1.571,1.597-1.571h7.454V3.332H5.231c-1.468,0-2.662,1.168-2.662,2.636v12.778c0,1.468,1.194,2.688,2.662,2.688  h12.778c1.468,0,2.662-1.221,2.662-2.688v-7.428h-1.065V18.746z"/><path d="M20.807,3.17c-0.804-0.805-2.207-0.805-3.012,0l-7.143,7.143c-0.068,0.068-0.117,0.154-0.14,0.247L9.76,13.571  c-0.045,0.181,0.008,0.373,0.14,0.506c0.101,0.101,0.237,0.156,0.376,0.156c0.043,0,0.086-0.005,0.129-0.016l3.012-0.753  c0.094-0.023,0.179-0.072,0.247-0.14l7.143-7.143c0.402-0.402,0.624-0.937,0.624-1.506S21.21,3.572,20.807,3.17z M13.016,12.467  l-2.008,0.502l0.502-2.008l5.909-5.909l1.506,1.506L13.016,12.467z M20.054,5.428l-0.376,0.376l-1.506-1.506l0.376-0.376  c0.402-0.402,1.104-0.402,1.506,0c0.201,0.201,0.312,0.468,0.312,0.753C20.366,4.96,20.255,5.227,20.054,5.428z"/>
            </svg>
        </button>
        <drop-down-menu :id="ticket.id" v-if="activeSubmenu"></drop-down-menu>
    </td>
  </tr>
</template>

<script>
// Use the moment library to format the time, we don't need to show it that exact I think...
// https://momentjs.com/
import moment from 'moment';
import DropDownMenu from '../components/DropDownMenu.vue'; // DropDown component

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
            ...this.ticket,
            activeSubmenu: false
        }
    },
    components: {
        DropDownMenu
    },
    methods: {
        // Format the date
        formatDate(date) {
            return moment(date).format('YYYY-MM-DD')
        },
        // Format the loan amounts to a more human readable version, omit decimals
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

.ticket-row {
    position: relative;
    background-color: white;

    @include respond-above("large") {
        border-bottom: 1px solid $color-border;
        position: static;

        &:hover {
            .ticket-section {
                background-color: #f8f8f8;
            }
        }
    }
}

.ticket-section {
    padding: 10px;
    font-size: 85%;

    // On smaller viewports, lets display our data as a list
    @include respond-below("large") {
        display: flex;
        border-bottom: 1px solid $color-border;

        &[data-label] {
            &:before {
                content: attr(data-label);
                width: 50%;
            }
        }
     }
    
    @include respond-above("medium") {
        font-size: 90%;
    }

    @include respond-above("large") {
        font-size: 95%;
        padding: 1.25vw;

        &.align-right {
            text-align: right;
        }

        &.align-center {
            text-align: center;
        }
        & + .ticket-section {
            border-left: 1px solid $color-border;
        }
    }

    @include respond-above("huge") {
        padding: 1.25rem;
        font-size: 100%;
    }
}

.ticket-section--button {
    padding: 0;

    @include respond-above("large") {
        position: relative;
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
    position: absolute;
    right: 10px;
    top: 10px;

    @include respond-above("large") {
        position: unset;
    }

    svg {
        width: 25px;
    }
}


</style>

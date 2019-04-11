<template>
  <div class="ticket">
    <div class="ticket-section ticket-id">{{ ticket.id }}</div>
    <div class="ticket-section ticket-status">{{ ticket.status }}</div>
    <div class="ticket-section ticket-requestedAmount">{{ ticket.requested_amount }}</div>
    <div class="ticket-section ticket-grantedAmount">{{ ticket.granted_amount }}</div>
    <div class="ticket-section ticket-startDate">{{ ticket.start_date }}</div>
    <div class="ticket-section ticket-lastActionDate">{{ ticket.last_action_date }}</div>
    <div class="ticket-section ticket-customer">{{ ticket.customer.name }}</div>
    <div class="ticket-section ticket-button">...</div>
  </div>
</template>

<script>
export default {
    name: 'Ticket',
    props: ['ticket']
  }
</script>

<style lang="scss" scoped>
.ticket {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
    background-color: white;
    padding:  20px 10px;
    font-size: 15px;

    & + .ticket {
        margin-top: 10px;
    }
}
</style>

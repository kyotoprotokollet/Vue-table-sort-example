<template>
    <nav class="dropdown-menu">
        <ul>
            <li><a :href="`/tickets/${id}`">Redigera ärende</a></li>
            <li><a href="#">Klarmarkera</a></li>
            <li><a href="#">Släng</a></li>
        </ul> 
    </nav>
</template>

<script>
export default {
    name: 'DropDownMenu',
    props: ['id']
  }
</script>

<style lang="scss" scoped>
@import "./src/assets/scss/_globals.scss";

.dropdown-menu {
    position: absolute;
    top: 40px;
    right: 0;
    z-index: 1;
    width: max-content;
    background-color: white;
    box-shadow: 0 7px 11px rgba(0,0,0,.09);

    li + li {
        border-top: 1px solid $color-border;
    }

    a {
        display: block;
        padding: 10px 15px;
    }
}
</style>

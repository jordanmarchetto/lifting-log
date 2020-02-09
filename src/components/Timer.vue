<template>
        <div class='timer-wrap'>
            <h2 v-bind:class="{ red: (value<10)}" >{{value}}</h2>
        </div>
</template>

<script lang="ts">
export default {
    name: 'Timer',
    props: {
        value: String
    }
}
</script>

<style scoped lang="scss">
.timer-wrap{
    width: 100%;
    background: white;
    display:inline-block;
    h2{
        font-size: 3em;
        background: white;
        width: 100%;
    }
    .red{color: red;}
}
</style>

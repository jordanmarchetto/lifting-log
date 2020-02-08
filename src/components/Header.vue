<template>
    <header>
        <div class='header-wrap'>
            <h1>{{ title }}</h1>
            <md-button class="md-icon-button md-dense md-raised" @click="$emit('reload')">
                <md-icon>cached</md-icon>
            </md-button>
    
        </div>
    </header>
</template>

<script lang="ts">
export default {
    name: 'Header',
    props: {
        title: String
    }
}
</script>

<style scoped lang="scss">
header {
    background: #0d47a1;
    color: white;
    border-bottom: 1px solid var(--gray);
    box-shadow: 1px 1px 3px 0px var(--dark-gray);
    .header-wrap {
        display: inline-block;
        text-align: left;
        width: 100%;
        padding: 0 1em;
        display: flex;
        align-items: center;
        justify-content: space-between;
    }
}
</style>

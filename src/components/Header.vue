<template>
    <header>
        <div class='header-wrap'>
            <h1>{{ title }}</h1>
            <slot v-if="!api_running">
                <md-button class="md-icon-button md-dense md-raised" @click="$emit('reload')">
                    <md-icon>cached</md-icon>
                </md-button>
            </slot>
        </div>
    
        <slot v-if="api_running">
            <md-progress-bar class="" md-mode="query" md-theme="light"></md-progress-bar>
        </slot>
    
    </header>
</template>

<script lang="ts">
export default {
    name: 'Header',
    props: {
        title: String,
        api_running: Boolean
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

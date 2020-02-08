<template>
    <header>
        <div class='header-wrap'>
            <h1>{{ title }}</h1>
        </div>
    </header>
</template>

<script>
export default {
    name: 'Header',
    props: {
        title: String
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
header {
    background: #0d47a1;
    color: white;
    border-bottom: 1px solid var(--gray);
    box-shadow: 1px 1px 3px 0px var(--dark-gray);
}

.header-wrap {
    display: inline-block;
    text-align: left;
    width: 100%;
    padding: 0 1em;
}
</style>

<template>
    <footer>
        <div class='footer-wrap'>
            <p>&copy; {{ new Date().getFullYear() }} Jordan Marchetto </p>
        </div>
    </footer>
</template>

<script>
export default {
    name: 'Footer',
    props: {
        title: String
    }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
footer {
    border-top: 1px solid var(--gray);
    background-color: var(--primary-light);
    color: white;
}

.footer-wrap {
    display: inline-block;
    width: 100%;
    text-align: left;
    padding: 0 1em;
}
</style>

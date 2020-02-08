<template>
    <footer>
        <div class='footer-wrap'>
            <p>&copy; {{ new Date().getFullYear() }} Jordan Marchetto </p>
        </div>
    </footer>
</template>

<script lang="ts">
export default {
    name: 'Footer',
    props: {
        title: String
    }

}
</script>

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

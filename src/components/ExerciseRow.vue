<template>
    <p> {{ id }} - {{name}} - {{rest_time}} seconds <a @click="showExerciseDetails">show details</a></p>
</template>

<script>
export default {
    name: 'ExerciseRow',
    props: {
        id: Number,
        name: String,
        rest_time: String,
        type: String
    },
    methods: {
        showExerciseDetails: function() {
            console.log("showing these deets: " + this.id)
            this.$emit('show-details', this.id);
        }

    }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

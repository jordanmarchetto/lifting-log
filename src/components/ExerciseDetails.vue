<template>
    <div>
        <h2>Details</h2>
        <p>Exercise we're looking at: {{ exercise.id }} - {{exercise.name}} - {{exercise.rest_time}} seconds </p>
        <div v-if="relevantRecords && relevantRecords.length>0">
            <div v-for="entry in relevantRecords" v-bind:key="entry.id">
                ex: {{entry.exercise}}, created_at: {{entry.created_at}}, reps: {{entry.reps}}, weight: {{entry.weight}}
            </div>
        </div>
        <div v-else>
            no records found
        </div>

        <h2>
            delete exercise
        </h2>
    </div>
</template>

<script>
export default {
    name: 'ExerciseDetails',
    props: {
        exercise: Object,
        records: Array
    },
    computed: {
        relevantRecords: function() {
            console.log("only keeping entries that matter");
            let recs = this.records.filter(record => record.exercise === this.exercise.id);
            return recs;

        }
    },
    mounted() {},
    methods: {},
    data: function() {
        return {}
    }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

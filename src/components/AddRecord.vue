<template>
    <div>
    
        <div v-if="loading">
            Loading ...
        </div>
        <div v-else>
    
            <form @submit.prevent="submitRecord">
                <p>Exercise we're looking at: {{ exercise.id }} - {{exercise.name}} - {{exercise.rest_time}} seconds </p>
                <input type="text" name="reps" v-model="newRecord.reps" placeholder="Reps" />
                <input type="text" name="weight" v-model="newRecord.weight" placeholder="Weight" />
    
                <button>Submit</button>
            </form>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
export default {
    name: 'AddRecord',
    props: {
        exercise: Object
    },
    data: function() {
        return {
            loading: false,
            newRecord: {}
        }
    },
    methods: {
        submitRecord() {
            console.log("sending exercise to api")
            this.newRecord.exercise = this.exercise.id;
            console.log(this.newRecord);
            this.$emit("record-added", this.newRecord);
            this.loading = true;
            axios
                .post('https://api.jmar.dev/lifting-log/exercise-records', this.newRecord)
                .then(() => {
                    console.log("call done, now emit something to parent")
                    this.newRecord = {};
                })
                .catch(error => {
                    console.error(error)
                })
                .finally(() => {
                    this.loading = false
                    console.log("api call complete.")
                })

            return;

        }


    }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

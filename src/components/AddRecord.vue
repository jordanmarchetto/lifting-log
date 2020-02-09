<template>
    <div class="add-record-wrap">
    
        <div v-if="loading" class="loading-overlay">
            <md-progress-spinner :md-diameter="80" :md-stroke="10" class="md-accent" md-mode="indeterminate"></md-progress-spinner>
        </div>
    
    
        <div class="top-row">
            <h2>Add Set</h2>
            <md-button @click="deleteExercise" class="md-accent">Delete Exercise</md-button>
        </div>
        <div class="form-wrap">
            <form novalidate class="md-layout" @submit.prevent="submitRecord">
                <div class="md-layout md-gutter md-layout-item md-size-100">
                    <slot v-if="exercise.type==='weight'">
                        <div class="md-layout-item md-xsmall-size-100 md-size-45">
                            <md-field>
                                <label>Reps</label>
                                <md-input v-model="newRecord.reps" type="number" placeholder="Reps"></md-input>
                            </md-field>
                        </div>
    
                        <div class="md-layout-item md-xsmall-size-100 md-size-45">
                            <md-field>
                                <label for="type">Weight</label>
                                <md-input v-model="newRecord.weight" type="number" placeholder="Weight"></md-input>
                            </md-field>
                        </div>
                    </slot>
    
                    <slot v-else>
                        <div class="md-layout-item md-xsmall-size-100 md-size-45">
                            <md-field>
                                <label for="type">Distance/Time</label>
                                <md-input v-model="newRecord.distance" type="number" placeholder="Distance/Time"></md-input>
                            </md-field>
                        </div>
                        <div class="md-layout-item md-xsmall-size-100 md-size-45">
                            <md-field>
                                <label for="unit">Unit</label>
                                <md-select v-model="newRecord.unit" name="unit" id="unit">
                                    <md-option value="m">m</md-option>
                                    <md-option value="km">km</md-option>
                                    <md-option value="mi">mi</md-option>
                                    <md-option value="yd">yd</md-option>
                                    <md-option value="s">s</md-option>
                                    <md-option value="min">min</md-option>
                                    <md-option value="hr">hr</md-option>
                                </md-select>
                            </md-field>
                        </div>
                    </slot>
    
                    <div class="md-layout-item md-size-10 ">
                        <md-button class="md-icon-button md-dense md-raised md-accent" @click="submitRecord">
                            <md-icon>arrow_forward</md-icon>
                        </md-button>
                    </div>
                </div>
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
        //confirm, then tell the parent
        deleteExercise: function() {
            if (confirm("Are you sure?")) {
                this.$emit("delete-exercise", this.exercise.id);
            }
        },
        //submit the form and alert the parent
        submitRecord() {
            //add in the right exercise id
            this.newRecord.exercise = this.exercise.id;

            //add in the right date
            //my server uses this format "2020-02-10 04:03:22";
            //so, just recreate that
            const date = new Date();
            let dateString = date.getFullYear() + "-" + (date.getMonth() + 1) + "-" + date.getDate();
            dateString += " " + date.getHours() + ":" + date.getMinutes() + ":" + ((date.getSeconds() < 10) ? "0" + date.getSeconds() : date.getSeconds());
            this.newRecord.created_at = dateString;

            //alert the parent
            this.$emit("record-added", this.newRecord);

            //check if we're in offline mode
            if (process.env.VUE_APP_OFFLINE === "true") {
                this.newRecord = {};
                this.loading = false
                return;
            }

            //otherwise send the record to the api
            //this.loading = true;
            this.$emit("api-started");
            axios
                .post('https://api.jmar.dev/lifting-log/exercise-records', this.newRecord)
                .then(() => {
                    //make a copy of the form, for easier re-submission of sets
                    //this.newRecord = JSON.parse(this.newRecord.stringify())
                })
                .catch(error => {
                    console.error(error)
                })
                .finally(() => {
                    this.loading = false
                    this.$emit("api-stopped");
                })
            return;
        }
    }
}
</script>

<style scoped lang="scss">
.add-record-wrap {
    padding: 0 1em;
    position: relative;
    overflow: hidden;
    .loading-overlay {
        background: white;
        position: absolute;
        width: 100%;
        height: 100%;
        opacity: .9;
        z-index: 10;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .top-row {
        text-align: right;
        padding: 1em;
        display: flex;
        justify-content: space-between;
        h2 {
            margin: 0;
        }
        a.delete {
            color: var(--error);
        }
    }
}
</style>

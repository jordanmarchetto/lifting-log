<template>
    <div id="app">
        <Header v-bind:title="APP_NAME" v-on:reload="fetchAllData" v-bind:api_running="api_running" />
    
        <main>
            <section v-if="!activeExercise">
                <div v-if="loadingExercises" class="loading-screen">
                    <h3>
                        Fetching data ...
                    </h3>
                    <md-progress-spinner :md-diameter="100" :md-stroke="10" class="md-accent" md-mode="indeterminate"></md-progress-spinner>
                </div>
                <div v-else>
                    <div class="scrollable-exercises">
                        <!-- list of exercises -->
                        <ExerciseRow v-for="exercise in exercises" v-bind:key="exercise.id" v-bind="exercise" v-on:show-details="showExerciseDetails" />
                    </div>
    
                    <!-- add new exercise form -->
                    <AddExercise v-on:exercise-added="updateExercises" v-on:api-started="running_api=true" v-on:api-stopped="running_api=false" />
                </div>
                <!-- end loading if/else -->
            </section>
    
            <!-- show exercise details -->
            <section v-else>
                <Timer v-bind:value="timer_value" v-if="timerRunning"/>
                <ExerciseDetails v-bind:exercise="activeExerciseRecords" v-bind:records="exerciseRecords" v-on:delete-record="deleteRecord" v-on:delete-exercise="deleteExercise" v-on:go-back="showExerciseDetails(null)" />
                <AddRecord v-bind:exercise="exercises.find(e => e.id===activeExercise.id)" v-on:record-added="updateRecords" v-on:delete-exercise="deleteExercise" v-on:api-started="running_api=true" v-on:api-stopped="running_api=false" />
            </section>
    
        </main>
        <Footer />
    </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'
import AddExercise from './components/AddExercise.vue'
import AddRecord from './components/AddRecord.vue'
import ExerciseRow from './components/ExerciseRow.vue'
import ExerciseDetails from './components/ExerciseDetails.vue'
import Timer from './components/Timer.vue'

import "normalize.css"
import 'vue-material/dist/vue-material.min.css'
import 'vue-material/dist/theme/default.css'

import VueMaterial from 'vue-material'
Vue.use(VueMaterial)

export default {
    name: 'app',
    components: {
        Header,
        Footer,
        ExerciseRow,
        ExerciseDetails,
        AddRecord,
        Timer,
        AddExercise
    },
    computed: {
        //keeps a filtered list of exercise records based on the active exercise id
        activeExerciseRecords: function() {
            return this.exercises.find(e => e.id === this.activeExercise.id);
        },
        APP_NAME: function() {
            return process.env.VUE_APP_NAME;
        },
        api_running: function() {
            return (this.loadingExercises || this.loadingRecords || this.running_api) ? true : false;
        },
        timer_value: function() {
            if (this.timerRunning) {
                return this.timerCurrent.toString();
            } else {
                return '';
            }
        }
    },
    methods: {
        //get all of the data from the api
        //runs in two api calls, since the api response is pretty slow
        fetchAllData: function() {
            if (process.env.VUE_APP_OFFLINE === "true") {
                console.log(process.env.VUE_APP_EXERCISE_DATA);
                this.exercises = JSON.parse(process.env.VUE_APP_EXERCISE_DATA);
                this.exerciseRecords = JSON.parse(process.env.VUE_APP_RECORD_DATA);
                this.loadingExercises = false;
                this.loadingRecords = false;
                return;
            }
            console.log("fetching from api");
            this.loadingExercises = true;
            axios
                .get('https://api.jmar.dev/lifting-log/exercises')
                .then(response => (this.exercises = response.data))
                .catch(error => {
                    console.error(error);
                })
                .finally(() => {
                    this.loadingExercises = false;
                    console.log("api call complete.")
                });

            console.log("fetching records api");
            this.loadingRecords = true;
            axios
                .get('https://api.jmar.dev/lifting-log/exercise-records')
                .then(response => (this.exerciseRecords = response.data.reverse()))
                .catch(error => {
                    console.error(error)
                })
                .finally(() => {
                    this.loadingRecords = false
                    console.log("api call complete.")
                });
        },
        //show the details of the selected exercise
        showExerciseDetails: function(id) {
            this.activeExercise = this.exercises.find(e => e.id === id);
        },
        //push the new entry to the UI
        updateRecords: function(record) {
            //new record received from AddRecord, make a deep copy
            const newRecord = JSON.parse(JSON.stringify(record))
            this.exerciseRecords.unshift(newRecord);
            console.log("got:");
            console.log(newRecord);
            console.log(this.exercises.find(e => e.id === newRecord.exercise));
            this.startTimer(this.exercises.find(e => e.id === newRecord.exercise).rest_time);
        },
        //push the new entry to the UI
        updateExercises: function(exercise) {
            //new exercise received from AddExercise, make a deep copy
            const newExercise = JSON.parse(JSON.stringify(exercise))
            this.exercises.push(newExercise);
        },
        //ex is the id to delete
        deleteExercise: function(ex) {
            this.showExerciseDetails(null);
            this.exercises = this.exercises.filter(e => e.id !== ex);
            if (process.env.VUE_APP_OFFLINE === "true") {
                return;
            }
            this.running_api = true;
            axios
                .delete('https://api.jmar.dev/lifting-log/exercises/' + ex)
                .catch(error => {
                    console.error(error);
                })
                .finally(() => {
                    console.log("api call complete.")
                    this.running_api = false;
                });
        },
        //ex is the id to delete
        deleteRecord: function(rec) {
            this.exerciseRecords = this.exerciseRecords.filter(r => r.id !== rec);
            if (process.env.VUE_APP_OFFLINE === "true") {
                return;
            }
            this.running_api = true;
            axios
                .delete('https://api.jmar.dev/lifting-log/exercise-records/' + rec)
                .catch(error => {
                    console.error(error);
                })
                .finally(() => {
                    console.log("api call complete.")
                    this.running_api = false;
                });
        },
        startTimer: async function(value) {
            this.timerCurrent = value;
            this.timerRunning = true;
            const sleep = m => new Promise(r => setTimeout(r, m));


            while (this.timerCurrent > 0) {
                if (this.timerCurrent > 60) {
                    this.timerCurrent = this.timerCurrent - 1;
                    await sleep(1000);
                } else {
                    this.timerCurrent = (this.timerCurrent - 0.01).toFixed(2);
                    await sleep(10);
                }
            }
            this.timerRunning = false;
        }

    },
    mounted: function() {
        this.fetchAllData();
    },
    data: function() {
        return {
            timerCurrent: 0, //value of the timer
            timerRunning: false, //if the timer is running
            loadingExercises: true, //true/false - show spinner while api call running
            loadingRecords: false,
            running_api: false, //true/false - generic for any api call
            exercises: null, //array of all exercise names/types/etc
            exerciseRecords: null, //list of all exercise records
            activeExercise: null //current exercise to show
        }
    }
}
</script>

<style lang="scss">
//customize the material components
@import "~vue-material/dist/theme/engine"; // Import the theme engine
@include md-register-theme("default", ( primary: md-get-palette-color(blue, 900), // The primary color of your application
accent: md-get-palette-color(purple, 700) // The accent or secondary color
));
@include md-register-theme("light", ( primary: md-get-palette-color(blue, 50), // The primary color of your application
accent: md-get-palette-color(purple, 50) // The accent or secondary color
));
@import "~vue-material/dist/theme/all"; // Apply the theme
:root {
    --primary: #0d47a1;
    --primary-light: #5472d3;
    --primary-dark: #002171;
    --secondary: #6a1b9a;
    --secondary-light: #9c4dcc;
    --secondary-dark: #38006b;
    --light-gray: #F5F5F6;
    --gray: #E1E2E1;
    --dark-gray: #ccc;
    --error: #9c4dcc;
}

#app {
    font-family: 'Roboto', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    max-width: 700px;
    margin: 0 auto;
    border: 1px solid var(--dark-gray);
    background-color: var(--light-gray);
    box-shadow: 2px 2px 9px 1px var(--dark-gray);
}

body {
    background-color: var(--gray);
}

.scrollable-exercises {
    max-height: 600px;
    overflow-y: scroll;
}

.loading-screen {
    padding: 2em;
}

a {
    color: var(--secondary);
    &:hover {
        cursor: pointer;
    }
}
</style>

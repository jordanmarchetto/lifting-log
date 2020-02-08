<template>
    <div id="app">
        <Header title="Lifting Log" v-on:reload="fetchAllData" />
    
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
                    <AddExercise v-on:exercise-added="updateExercises" />
                </div>
                <!-- end loading if/else -->
            </section>
    
            <!-- show exercise details -->
            <section v-else>
                <ExerciseDetails v-bind:exercise="activeExerciseRecords" v-bind:records="exerciseRecords" />
                <AddRecord v-bind:exercise="exercises.find(e => e.id===activeExercise.id)" v-on:record-added="updateRecords" />
                <md-button class="md-icon-button md-dense md-raised md-primary" @click="showExerciseDetails(null)">
                    <md-icon>backspace</md-icon>
                </md-button>
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
        AddExercise
    },
    computed: {
        //keeps a filtered list of exercise records based on the active exercise id
        activeExerciseRecords: function() {
            return this.exercises.find(e => e.id === this.activeExercise.id);
        },
    },
    methods: {
        //get all of the data from the api
        //runs in two api calls, since the api response is pretty slow
        fetchAllData: function() {
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
            axios
                .get('https://api.jmar.dev/lifting-log/exercise-records')
                .then(response => (this.exerciseRecords = response.data))
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
            this.exerciseRecords.push(newRecord);
        },
        //push the new entry to the UI
        updateExercises: function(exercise) {
            //new exercise received from AddExercise, make a deep copy
            const newExercise = JSON.parse(JSON.stringify(exercise))
            this.exercises.push(newExercise);
        }
    },
    mounted: function() {
        this.fetchAllData();
    },
    data: function() {
        return {
            loadingExercises: true, //true/false - show spinner while api call running
            loadingRecords: false,
            exercises: null, //list of all exercise names/types/etc
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
accent: md-get-palette-color(purple, 700), // The accent or secondary color
jj: md-get-palette-color(purple, 700) // The accent or secondary color
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
}
</style>

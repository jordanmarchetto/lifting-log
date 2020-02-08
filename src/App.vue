<template>
    <div id="app">
        <Header title="Lifting Log" />
        <main>
            <section v-if="!activeExercise">
                <div v-if="loadingExercises" class="loading-screen">
                    <h3>
                        Fetching data ...
                    </h3>
                    <md-progress-spinner :md-diameter="100" :md-stroke="10" class="md-accent" md-mode="indeterminate"></md-progress-spinner>
                </div>
                <div v-else>
                    <div>
                        <ExerciseRow v-for="exercise in exercises" v-bind:key="exercise.id" v-bind="exercise" v-on:show-details="showExerciseDetails" />
                    </div>
    
                    <md-button class="md-icon-button md-dense md-raised md-accent" @click="fetchAllData">
                        <md-icon>cached</md-icon>
                    </md-button>
                    <AddExercise />
    
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


import { MdButton, MdIcon, MdProgress } from 'vue-material/dist/components'

Vue.use(MdProgress)
Vue.use(MdButton)
Vue.use(MdIcon)

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
        activeExerciseRecords: function() {
            return this.exercises.find(e => e.id === this.activeExercise.id);
        },
    },
    methods: {
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
        showExerciseDetails: function(id) {
            console.log("showing deetails: " + id)
            this.activeExercise = this.exercises.find(e => e.id === id);

        },
        updateRecords: function(record) {
            //new record received from AddExercise, make a deep copy
            const newRecord = JSON.parse(JSON.stringify(record))
            this.exerciseRecords.push(newRecord);
        }
    },
    mounted: function() {
        this.fetchAllData();
    },
    data: function() {
        return {
            loadingExercises: true,
            loadingRecords: false,
            exercises: null,
            exerciseRecords: null,
            activeExercise: null

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

.loading-screen{
  padding: 2em;
}

a {
    color: var(--secondary);
}
</style>

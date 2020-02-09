<template>
    <div class="add-excercise-wrap">
    
        <div v-if="loading" class="loading-overlay">
            <md-progress-spinner :md-diameter="80" :md-stroke="10" class="md-accent" md-mode="indeterminate"></md-progress-spinner>
        </div>
    
        <!-- heading -->
        <div class="heading-wrap" @click="toggleAddExerciseRow">
            <h2>
                Add New Exercise
            </h2>
            <md-button class="md-icon-button md-dense md-raised md-primary">
                <md-icon>{{iconName}}</md-icon>
            </md-button>
        </div>
    
        <div class="form-wrap" v-bind:class="{hidden:!expanded}">
            <form novalidate class="md-layout" @submit.prevent="submitExerise">
    
                <!-- row -->
                <div class="md-layout md-gutter md-layout-item md-size-100">
                    <div class="md-layout-item md-xsmall-size-100 md-size-45">
                        <md-field>
                            <label>Exercise Name</label>
                            <md-input v-model="newExercise.name" placeholder="Front Squat"></md-input>
                        </md-field>
                    </div>
    
                    <div class="md-layout-item md-xsmall-size-100 md-size-45">
                        <md-field>
                            <label>Notes</label>
                            <md-input v-model="newExercise.notes" placeholder="w/belt"></md-input>
                        </md-field>
                    </div>
                </div>
    
                <!-- row -->
                <div class="md-layout md-gutter md-layout-item md-size-100">
                    <div class="md-layout-item md-xsmall-size-100 md-size-45">
                        <md-field>
                            <label>Rest Time/Interval</label>
                            <md-input v-model="newExercise.rest_time" type="number" placeholder="90 seconds"></md-input>
                        </md-field>
                    </div>
    
                    <div class="md-layout-item md-xsmall-size-100 md-size-45">
                        <md-field>
                            <label for="type">Exercise Type</label>
                            <md-select v-model="newExercise.type" name="type" id="type">
                                <md-option value="weight">Weight</md-option>
                                <md-option value="endurance">Endurance</md-option>
                            </md-select>
                        </md-field>
                    </div>
    
                    <div class="md-layout-item md-size-10">
                        <md-button class="md-icon-button md-dense md-raised md-accent" @click="submitExerise">
                            <md-icon>arrow_forward</md-icon>
                        </md-button>
                    </div>
                </div>
            </form>
        </div>
    </div>
</template>

<script lang="ts">
import axios from 'axios'
export default {
    name: 'AddExercise',
    props: {},
    data: function() {
        return {
            loading: false, //true/false, whether or not to show loading overlay
            expanded: false, //true/false, whether or not this component is expanded
            newExercise: {} //model for form/new exercise
        }
    },
    methods: {
        submitExerise() {
            //sanitize the name, make each word start with capital letter
            const newName = this.newExercise.name.split(" ").map(w => w.slice(0, 1).toUpperCase() + w.slice(1, )).join(" ");
            this.newExercise.name = newName;

            //tell parent to add this exercise
            this.$emit("exercise-added", this.newExercise);

            //bail out if we're offline
            if (process.env.VUE_APP_OFFLINE === "true") {
                this.newExercise = {};
                this.loading = false
                return;
            }

            //run api cmd
            this.$emit("api-started");
            this.loading = true;
            axios
                .post('https://api.jmar.dev/lifting-log/exercises', this.newExercise)
                .then(() => {
                    //reset the data, clearing the form
                    this.newExercise = {};
                })
                .catch(error => {
                    console.error(error)
                })
                .finally(() => {
                    this.loading = false
                    this.$emit("api-stopped");
                })
        },
        toggleAddExerciseRow() {
            this.expanded = !this.expanded;
        }
    },
    computed: {
        iconName: function() {
            return (this.expanded) ? "close" : "add";
        }
    }
}
</script>

<style scoped lang="scss">
.add-excercise-wrap {
    background-color: var(--light-gray);
    border-top: 1px solid var(--dark-gray);
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
    .heading-wrap {
        display: flex;
        justify-content: space-between;
        align-items: center;
        &:hover {
            cursor: pointer;
        }
    }
}

.hidden {
    display: none;
}
</style>

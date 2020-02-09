<template>
    <div>
        <div class="title-bar">
    
            <div class="title-wrap">
                <md-button class="md-icon-button md-dense" @click="$emit('go-back')">
                    <md-icon>chevron_left</md-icon>
                </md-button>
                <h2>{{exercise.name}}</h2>
                <span v-html="notesText"></span>
            </div>
            <md-button class="md-icon-button md-dense md-accent" disabled>
                <md-icon v-if="exercise.type==='endurance'" class="md-accent">directions_run</md-icon>
                <md-icon v-else class="md-accent">fitness_center</md-icon>
            </md-button>
    
        </div>
        <div v-if="relevantRecords && relevantRecords.length>0" class="record-list">
            <SetDetails v-for="entry in relevantRecords" v-bind:key="entry.id" v-bind:set="entry" v-bind:exercise="exercise" v-on:delete-record="$emit('delete-record', entry.id)" />
        </div>
        <div v-else>
            No records found.
        </div>
        <!--
                    <div class="delete-row">
                        <a href @click="deleteExercise" class="delete">Delete Exercise</a>
                        </div>
                        -->
    
    </div>
</template>

<script lang="ts">
import SetDetails from './SetDetails.vue'

export default {
    name: 'ExerciseDetails',
    props: {
        exercise: Object,
        records: Array
    },
    components: {
        SetDetails
    },
    computed: {
        //filter out all the records that aren't for this exercise
        relevantRecords: function() {
            let recs = this.records.filter(record => record.exercise === this.exercise.id);
            return recs;
        },
        //turn time into a sentence: XX seconds rest
        restText: function() {
            let time = parseInt(this.exercise.rest_time);
            if (time && time > 0) {
                return time + " seconds rest";
            } else {
                return '&nbsp;';
            }
        },
        //wrap notes in ()'s
        notesText: function() {
            if (this.exercise.notes) {
                return "(" + this.exercise.notes + ")";
            } else {
                return '&nbsp;';
            }
        }

    },
    mounted() {},
    methods: {
        //confirm, then tell the parent
        deleteExercise: function() {
            if (confirm("Are you sure?")) {
                this.$emit("delete-exercise", this.exercise.id);
            }
        }
    },
    data: function() {
        return {}
    }

}
</script>

<style scoped lang="scss">
.title-bar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 1em;
    .title-wrap {
        display: flex;
        align-items: center;
        h2 {
            margin: 0 0.5em;
            display: inline;
        }
    }
}

.record-list {
    max-height: 460px;
    overflow-y: scroll;
}

.delete-row {
    text-align: right;
    padding: 1em;
    float: right;
    a.delete {
        color: var(--error);
    }
}
</style>

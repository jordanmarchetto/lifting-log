<template>
    <div class="exercise-wrap" @click="showExerciseDetails">
        <div class="title-wrap">
            <md-button class="md-icon-button md-dense md-raised md-accent">
                <md-icon v-if="type==='endurance'">directions_run</md-icon>
                <md-icon v-else>fitness_center</md-icon>
            </md-button>
    
            <h2>{{name}}</h2>
    
            <span v-html="notesText"></span>
        </div>
        <p><span v-html="restText"></span></p>
    </div>
</template>

<script lang="ts">
export default {
    name: 'ExerciseRow',
    props: {
        id: Number,
        name: String,
        notes: String,
        rest_time: String,
        type: String
    },
    methods: {
        //tell parent to expand this exercise
        showExerciseDetails: function() {
            this.$emit('show-details', this.id);
        }
    },
    computed: {
        //turn time into a sentence: XX seconds rest
        restText: function() {
            let time = parseInt(this.rest_time);
            if (time && time > 0) {
                let rest_interval_text = (this.type === "weight")?" seconds rest":" second interval";
                return time + rest_interval_text;
            } else {
                return '&nbsp;';
            }
        },
        //wrap notes in ()'s
        notesText: function() {
            if (this.notes) {
                return "(" + this.notes + ")";
            } else {
                return '&nbsp;';
            }
        }
    }
}
</script>

<style scoped lang="scss">
.exercise-wrap {
    border: 1px solid var(--light-gray);
    width: 100%;
    background: white;
    display: flex;
    padding: 0 1em;
    justify-content: space-between;
    align-items: center;
    &:hover {
        cursor: pointer;
        box-shadow: inset 0px 0px 0px 1px var(--dark-gray);
        h2 {
            opacity: .8;
        }
    }
}

.title-wrap {
    display: flex;
    align-items: center;
    h2 {
        margin: 0 0.5em;
    }
}
</style>
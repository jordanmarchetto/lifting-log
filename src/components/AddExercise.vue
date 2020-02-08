<template>
    <div class="add-excercise-wrap">
    
        <div v-if="loading">
            Loading ...
        </div>
        <div v-else>
    
            <form @submit.prevent="submitExerise">
                <input type="text" name="name" v-model="newExercise.name" />
                <input type="text" name="rest_time" v-model="newExercise.rest_time" />
                <select name="type" v-model="newExercise.type">
            <option value="weight">Weight</option>
            <option value="endurance">Endurance</option>
          </select>
                <button>Submit</button>
            </form>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
export default {
    name: 'AddExercise',
    props: {},
    data: function() {
        return {
            loading: false,
            newExercise: {}
        }
    },
    methods: {
        submitExerise() {
            console.log("sending exercise to api")
            console.log(this.newExercise);
            this.loading = true;
            axios
                .post('https://api.jmar.dev/lifting-log/exercises', this.newExercise)
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
.add-excercise-wrap{
  background-color: var(--gray);
  }
</style>

<template>
    <div class='set-wrap' @click="toggleDetails">
        <div class="summary">
            <div class="left col">
                <span class="date">{{date}}</span>
                <span class="time">{{time}}</span>
            </div>
            <div class="center col">
                <span class="reps"><span class="num">{{set.reps}}</span> reps</span>
            </div>
            <div class="right col">
                <span class="weight"><span class="num">{{set.weight}}</span> lbs.</span>
            </div>
        </div>
        <div class="details" v-if="expanded">
            <md-button @click="$emit('delete-record')" class="md-accent">Delete Record</md-button>
        </div>
    </div>
</template>

<script lang="ts">
export default {
    name: 'SetDetails',
    props: {
        set: Object
    },
    computed: {
        date: function() {
            //format: dd-mm-yyyy
            const full_date = this.set.created_at.split(" ")[0];
            //converts to: dd/mm/yy
            let new_date = full_date.split("-").slice(1, ).map(n => parseInt(n)).join("/") + "/" + full_date.split("-")[0].slice(2, );
            //converts to: dd/mm
            new_date = full_date.split("-").slice(1, ).map(n => parseInt(n)).join("/");
            return new_date;
        },
        time: function() {
            return this.set.created_at.split(" ")[1];
        }
    },
    methods: {
        toggleDetails: function() {
            this.expanded = !this.expanded;
        }
    },
    data: function() {
        return {
            expanded: false
        }
    }
}
</script>

<style scoped lang="scss">
.set-wrap {
    background: white;
    border: 1px solid var(--light-gray);
    .summary {
        display: flex;
        align-items: flex-end;
        justify-content: space-between;
        padding: 1em;
        width: 100%;
        .col {
            width: 33%;
        }
        .left {
            display: flex;
            align-items: flex-end;
            .date {
                font-weight: bold;
                font-size: 2em;
                text-align: right;
                width: 40%;
            }
            .time {
                width: 60%;
                padding-left: .5em;
                text-align: left;
            }
        }
    }
    &:hover {
        cursor: pointer;
        box-shadow: inset 0px 0px 0px 1px var(--dark-gray);
    }
    .reps {
        .num {
            font-size: 2.5em;
            font-weight: bold;
        }
    }
    .weight {
        .num {
            font-size: 2.5em;
            font-weight: bold;
        }
    }
}
</style>

<template>
    <div class='set-wrap'>
        <div class="left">
            <span class="date">{{date}}</span>
            <span class="time">{{time}}</span>
        </div>
        <div class="right">
            {{set.reps}} reps {{set.weight}} lbs.
    
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
            let new_date = full_date.split("-").slice(1,).map(n => parseInt(n)).join("/") + "/" + full_date.split("-")[0].slice(2,);
            return new_date;
        },
        time: function() {
            return this.set.created_at.split(" ")[1];
        }
    }
}
</script>

<style scoped lang="scss">
.set-wrap {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 1em;
    background: white;
    border: 1px solid var(--light-gray);
    width: 100%;
    &:hover {
        //cursor: pointer;
        box-shadow: inset 0px 0px 0px 1px var(--dark-gray);
    }
    .left {
        display: flex;
        align-items: flex-start;
        flex-direction: column;
    }

    .date{
        font-weight: bold;
    }
}
</style>

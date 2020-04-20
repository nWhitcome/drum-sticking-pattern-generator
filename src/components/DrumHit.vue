<template>
    <div v-bind:class="(hover == index || hover == -1) ? 'backBox' : 'backBoxSmall'">
        <p v-bind:class="(hover == index || hover == -1) ? 'centerLetter' : 'centerLetterSmall'">{{ hitType }}</p>
    </div>
</template>

<script>
export default {
    name: 'DrumHit',
    props: ['hitType', 'active', 'hover', 'index'],
    data() {
        return{
            hitLetter: this.hitType,
        }
    },
    watch:{
        data(val){
            this.hitLetter=val
        }
    },
}
</script>

<style lang="scss">
.backBox {
    background-color: #DDD;
    color: #333;
    width: 4em;
    height: 6em;
    border-radius: 1em;
    margin: 1em;
    z-index: 1;
    transition: .2s;
    cursor: pointer;
}

.backBox:active{
    background-color: #CCC;
    transition: 0s;
}

.backBoxSmall {
    background-color: #EEE;
    color: #AAA;
    width: 3.5em;
    height: 5.5em;
    border-radius: 1em;
    margin: 1.25em;
    z-index: 1;
    transition: .2s;
    cursor: pointer;
}

.centerLetter {
    font-size: 1.9em;
    width: 100%;
    height: 100%;
    margin: 0px;
    transition: .2s;
    display: flex;
    align-items: center;
    justify-content: center;
}

.centerLetterSmall {
    font-size: 1.8em;
    width: 100%;
    height: 100%;
    margin: 0px;
    transition: .2s;
    display: flex;
    align-items: center;
    justify-content: center;
}
</style>
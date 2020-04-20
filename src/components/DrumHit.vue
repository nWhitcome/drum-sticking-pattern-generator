<template>
    <div v-bind:class="(hover == index || hover == -1) ? 'backBox' : 'backBoxSmall'">
        <p class="centerLetter">{{ hitType }}</p>
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
    margin: 10px;
    z-index: 1;
    transition: .1s;
    cursor: pointer;
}

.backBox:active{
    background-color: #CCC;
    transition: 0s;
}
.backBoxSmall {
    background-color: #EEE;
    color: #AAA;
    width: 4em;
    height: 6em;
    border-radius: 1em;
    margin: 10px;
    z-index: 1;
    transition: .1s;
    cursor: pointer;
}
.centerLetter {
    font-size: 30px;
}
</style>
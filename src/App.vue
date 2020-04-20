<template>
  <div id="app">
    <!--<h1>Metronome: <input class="textInput" type="number" min="10" max="300" v-model="metronome">BPM</h1>-->
    <b style="font-size: 26px; margin: 10px; color: #333">{{ numHitsVar }}</b>
    <div class="slideContainer">
      <input type="range" min="3" max="15" v-model="numHitsVar" class="slider" id="numHits">
    </div>
    <div class="generalBox">
      <div class="hitsBox">
        <DrumHit class="noselect" 
        v-for="(n, i) in patternArray" 
        :hitType='patternArray[i]' 
        v-show="i < numHitsVar" 
        :key="'item' + i + keyUpdate"
        :hover='highlighted'
        :index='i'
        @mouseover.native="highlighted = i"
        @mouseleave.native="highlighted = -1"
        @click.native="changeLetter(i)"></DrumHit>
      </div>
      <div @click="genPattern()" id="genButton" class="noselect">Generate</div>
    </div>
  </div>
</template>

<script>
import DrumHit from './components/DrumHit.vue';
export default {
  name: 'app',
  components: { DrumHit },
  data () {
    return {
      numHitsVar: 5,
      possArray: ['L', 'R', 'K'],
      patternArray: [],
      highlighted: -1, 
      keyUpdate: 0,
      metronome: 120,
    }
  },
  methods: {
    genPattern: function () {
      this.patternArray = [];
      while(this.patternArray.length < 15){
        var letter = this.possArray[Math.floor(Math.random() * this.possArray.length)];
        var patLength = this.patternArray.length;
        if(patLength > 2){
          while(this.patternArray[patLength - 2].localeCompare(letter) == 0 && this.patternArray[patLength - 3].localeCompare(letter) == 0){
            letter = this.possArray[Math.floor(Math.random() * this.possArray.length)];
          }
        }
        this.patternArray.push(letter);
      }
    },
    changeLetter: function(i){
      //var audio = new Audio("./src/audio/mid.mp3");
      //audio.play();
      this.patternArray[i] = this.possArray[(this.possArray.indexOf(this.patternArray[i]) + 1) % this.possArray.length];
      this.keyUpdate++;
    },
  },
  beforeMount() {
    this.genPattern();
  },
}
</script>

<style lang="scss">
$blue-general: #2471a3;
$blue-dark: #1c5a82;

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  justify-content: center;
}

$button-height: 2em;
#genButton {
  background-color: $blue-general;
  width: 8em;
  color: white;
  height: $button-height;
  cursor: pointer;
  border-radius: 1em;
  font-size: 1.5em;
  line-height: $button-height;
  margin: 20px auto;
  opacity: 0.8;
  -webkit-transition: .2s;
  transition: opacity .2s;
}

#genButton:active {
  -webkit-transition: 0s;
  transition: opacity 0s;
  background-color: $blue-dark;
}

.hitsBox, .generalBox {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}

.hitsBox {
  flex-direction: row;
}

.generalBox {
  flex-direction: column;
}

.noselect {
  -webkit-touch-callout: none; /* iOS Safari */
    -webkit-user-select: none; /* Safari */
     -khtml-user-select: none; /* Konqueror HTML */
       -moz-user-select: none; /* Old versions of Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
            user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome, Opera and Firefox */
}

.slidecontainer {
  width: 100%; /* Width of the outside container */
  max-width: 500px;
}

/* The slider itself */
.slider {
  -webkit-appearance: none;  /* Override default CSS styles */
  appearance: none;
  width: 100%; /* Full-width */
  max-width: 500px;
  height: 2px; /* Specified height */
  background: #d3d3d3; /* Grey background */
  outline: none; /* Remove outline */
  opacity: 0.8; /* Set transparency (for mouse-over effects on hover) */
  -webkit-transition: .2s; /* 0.2 seconds transition on hover */
  transition: opacity .2s;
  margin: 10px 0 20px 0;
}

/* Mouse-over effects */
.slider:hover, #genButton:hover {
  opacity: 1; /* Fully shown on mouse-over */
}

.slider::-webkit-slider-thumb {
  -webkit-appearance: none; /* Override default look */
  appearance: none;
  width: 15px; /* Set a specific slider handle width */
  height: 15px; /* Slider handle height */
  background: $blue-general; /* Green background */
  cursor: pointer; /* Cursor on hover */
  border-radius: 50%;
}

.slider::-moz-range-thumb {
  width: 15px; /* Set a specific slider handle width */
  height: 15px; /* Slider handle height */
  background: $blue-general; /* Green background */
  cursor: pointer; /* Cursor on hover */
  border-radius: 50%;
}

.textInput {
  background-color: white;
  border: 0px solid black;
  color: #2c3e50;
  font-size: 26px;
  font-weight: bold;
  width: 70px;
}

h1 {
  font-size: 20px;
  font-weight: normal;
}
</style>
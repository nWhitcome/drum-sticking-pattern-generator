<template>
  <div id="app">
    <h1>
      Metronome:
      <input class="textInput" type="number" min="10" max="300" v-model="metronome.bpm" />BPM
    </h1>
    <div class="arrowHolder noselect">
        <div class="arrowInnerBox">
          <i @click="playPause()" style="cursor: pointer">{{ getPlayPauseIcon }}</i>
        </div>
      </div>
    <b style="font-size: 26px; margin: 10px; color: #333">{{ numHitsVar }}</b>
    <div class="slideContainer">
      <input
        type="range"
        min="3"
        v-bind:max="maxHits"
        v-model="numHitsVar"
        class="slider"
        id="numHits"
      />
    </div>
    <div class="generalBox">
      <div class="hitsBox">
        <DrumHit
          class="noselect"
          v-for="(n, i) in patternArray"
          :hitType="patternArray[i]"
          v-show="i < numHitsVar"
          :key="'item' + i + keyUpdate"
          :hover="highlighted"
          :index="i"
          @mouseover.native="highlighted.one = i"
          @mouseleave.native="highlighted.one = -1"
          @click.native="changeLetter(i)"
        ></DrumHit>
      </div>
      <div @click="genPattern()" id="genButton" class="noselect">Generate</div>
    </div>
  </div>
</template>

<script>
import DrumHit from "./components/DrumHit.vue";
export default {
  name: "app",
  components: { DrumHit },
  data() {
    return {
      numHitsVar: 5,
      possArray: ["L", "R", "K"],
      patternArray: [],
      highlighted: {
        one: -1,
        two: -1
      },
      keyUpdate: 0,
      metronome: {
        bpm: 120,
        interval: null,
        index: 0,
      },
      isPlaying: false,
      maxHits: 15,
      audioFiles: {
        L: new Howl({ src: "src/audio/mid.mp3" }),
        R: new Howl({ src: "src/audio/high.mp3" }),
        K: new Howl({ src: "src/audio/low.mp3" }),
      }
    };
  },
  methods: {
    /*
     * Generates the sticking pattern and places it into an array. Makes sure there are no triplets of the same hit.
     */
    genPattern: function() {
      this.patternArray = [];
      while (this.patternArray.length < this.maxHits) {
        var letter = this.possArray[
          Math.floor(Math.random() * this.possArray.length)
        ];
        var patLength = this.patternArray.length;

        //Checks if the previous two items in the array match the one that is about to be added and changes it if it is
        if (patLength > 1) {
          if (
            this.patternArray[patLength - 2].localeCompare(letter) == 0 &&
            this.patternArray[patLength - 1].localeCompare(letter) == 0
          ) {
            var dupArray = this.possArray.slice();
            dupArray.splice(dupArray.indexOf(letter), 1);
            letter = dupArray[Math.floor(Math.random() * dupArray.length)];
          }
        }
        this.patternArray.push(letter);
      }
    },
    changeLetter: function(i) {
      //var audio = new Audio("./src/audio/mid.mp3");
      //audio.play();
      this.patternArray[i] = this.possArray[
        (this.possArray.indexOf(this.patternArray[i]) + 1) %
          this.possArray.length
      ];
      this.keyUpdate++;
    },
    playPause: function(){
      if(this.isPlaying){
        this.isPlaying = false;
      }
      else{
        this.isPlaying = true;
      }
    },
    playSound(){
      var nextSound = this.highlighted.two + 1;
      if(this.highlighted.two < this.numHitsVar - 1){
        this.highlighted.two++;
      }
      else{
        nextSound = 0;
        this.highlighted.two = 0;
      }
      if(this.patternArray[nextSound].localeCompare("L") == 0){
        this.audioFiles.L.play();
      }
      else if(this.patternArray[nextSound].localeCompare("R") == 0){
        this.audioFiles.R.play();
      }
      else if(this.patternArray[nextSound].localeCompare("K") == 0){
        this.audioFiles.K.play();
      }
    }
  },
  computed: {
    getPlayPauseIcon: function(){
      if(this.isPlaying){
        clearInterval(this.metronome.interval);
        this.metronome.interval = setInterval(() => {this.playSound()}, (60 * 1000)/(this.metronome.bpm));
        return "pause";
      }
      else{
        this.highlighted.two = -1;
        clearInterval(this.metronome.interval);
        return "play_arrow";
      }
    }
  },
  beforeMount() {
    this.genPattern();
  }
};
</script>

<style lang="scss">
$blue-general: #2471a3;
$blue-dark: #1c5a82;

@font-face {
  font-family: "Material_Icons";
  src: url("/src/fonts/Material_Icons.woff2") format("woff2");
  font-weight: normal;
  font-style: normal;
}

.arrowHolder{
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100px;
  font-size: 50px;
}

.arrowInnerBox{
  display: flex;
  align-items: center;
  justify-content: center;
  width: 60px;
  height: 60px;
  border-radius: 50%;
  transition: .2s;
  color: $blue_general;
}

.arrowInnerBox:hover{
  border: 4px solid $blue_general;
}

.arrowInnerBox:active{
  transition: 0s;
  border: 4px solid $blue_general;
  background-color: $blue_general;
  color: white;
}

i {
  font-family: "Material_Icons";
  font-style: normal;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
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
  -webkit-transition: 0.2s;
  transition: opacity 0.2s;
}

#genButton:active {
  -webkit-transition: 0s;
  transition: opacity 0s;
  background-color: $blue-dark;
}

.hitsBox,
.generalBox {
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
  z-index: 2;
  -webkit-appearance: none; /* Override default CSS styles */
  appearance: none;
  width: 100%; /* Full-width */
  max-width: 500px;
  height: 4px; /* Specified height */
  background: #d3d3d3; /* Grey background */
  outline: none; /* Remove outline */
  opacity: 0.8; /* Set transparency (for mouse-over effects on hover) */
  -webkit-transition: 0.2s; /* 0.2 seconds transition on hover */
  transition: opacity 0.2s;
  margin: 10px 0 20px 0;
}

/* Mouse-over effects */
.slider:hover,
#genButton:hover {
  opacity: 1; /* Fully shown on mouse-over */
}

.slider::-webkit-slider-thumb {
  z-index: 2;
  -webkit-appearance: none; /* Override default look */
  appearance: none;
  width: 20px; /* Set a specific slider handle width */
  height: 20px; /* Slider handle height */
  background: $blue-general; /* Green background */
  cursor: pointer; /* Cursor on hover */
  border-radius: 50%;
}

.slider::-moz-range-thumb {
  z-index: 2;
  width: 20px; /* Set a specific slider handle width */
  height: 20px; /* Slider handle height */
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
<template>
  <div class="app">
    <h1 class="header">Color Match</h1>
    <div class="background"></div>
    <IntroModal v-if="showModal" @close="closeIntro" />
    <div class="straighten">
      <div class="score" >Score: {{ score }}</div>
      <div class="timer" v-if="gameStart">{{ countdown }}</div>
      <div class="boxes">
        <div v-for="(box, index) in colorBoxes" :key="index" class="box" :style="{ backgroundColor: box.color }" @drop="dropColor(index)" @dragover.prevent :class="{ 'fade-out': box.fadeOut }"></div>
      </div>
    </div>
    <div class="straighten">
      <div class="color-words">
        <div v-for="(word, index) in colorWords" :key="index" class="word" :style="{ color: word.color }" draggable="true" @dragstart="dragStart(word)" @dragend="dragEnd" :class="{ 'fade-out': word.fadeOut }">{{ word.text }}</div>
      </div>
    </div>
    <div class="start-button" v-if="!gameStart" @click="startGame()">{{ buttonText }}</div>
  </div>
</template>

<script>
import ColorWord from '@/colorWord.js';
import ColorBox from '@/colorBox.js';
import IntroModal from '@/components/IntroModal.vue';

export default {

  components: {
    IntroModal
  },

  data() {
    return {
      colors: ['Black', 'Red', 'Green', 'Blue', 'Yellow', 'Fuchsia', 'Indigo', 'Aquamarine', 'Cyan', 'Magenta', 'Gray', 'Silver', 'Maroon', 'Olive', 'Navy', 'Purple', 'Teal', 'Orange', 'Lime', 'Pink', 'Brown', 'Gold'],
      colorWords: [],
      selectedWords: [],
      colorBoxes: [],
      showModal: false,
      gameStart: false,
      countdown: 20.00,
      finishedInTime: null,
      timer: null,
      buttonText: "Start Game",
      score: 0
    }
  },

  computed: {
    buttonText() {
      return this.score > 0 ? "Next Round" : "Start Game";
    },
  },

  methods: {
    startGame() {
      console.log("startGame method is called");
      this.addWords();
      this.addBoxes();
      this.colorWords = this.scrambleArray(this.selectedWords);
      this.gameStart = true;
      this.startTimer();
    },

    addWords() {
      let loops = 0;
      while (loops < 6) {
        let colorText = this.getRandomColorWord();
        let colorSelected = this.getRandomColorWord();
        if (!this.selectedWords.some(word => word.text === colorText || word.color === colorSelected) && colorText !== colorSelected) {
          const newColorText = new ColorWord(colorText, colorSelected)
          this.selectedWords.push(newColorText);
          loops++;
        }
      }
    },

    addBoxes() {
      this.selectedWords.forEach(element => {
        let newBoxColor = element.text;
        const newBox = new ColorBox(newBoxColor);
        this.colorBoxes.push(newBox);
      });
    },

    getRandomColorWord() {
      const randomIndex = Math.floor(Math.random() * this.colors.length);
      return this.colors[randomIndex];
    },

    scrambleArray(array) {
      console.log(array);
      return array.slice().sort(() => Math.random() - 0.5);
    },

    dragStart(word) {
      word.isDragging = true;
    },

    dragEnd() {
      this.colorWords.forEach(word => {
        word.isDragging = false;
      });
    },

    dropColor(boxIndex) {
      const draggedWord = this.colorWords.find(word => word.isDragging);
      if (!draggedWord) return;
      const boxColor = this.colorBoxes[boxIndex].color;

      if (draggedWord.text === boxColor) {
        draggedWord.fadeOut = true;
        this.colorBoxes[boxIndex].fadeOut = true;
        setTimeout(() => {
          const index = this.colorWords.indexOf(draggedWord);
          this.colorWords.splice(index, 1);
          this.colorBoxes.splice(boxIndex, 1);

          const allMatched = this.colorBoxes.every(box => box.matched);
          if (allMatched) {
          this.roundOver();
          
        }
        }, 500);
      }
      draggedWord.isDragging = false;
    },

    closeIntro() {
      console.log("Closing Modal");
      this.showModal = false;
    },

    startTimer() {
      if(this.gameStart){
        clearInterval(this.timer);
        this.timer = setInterval(() => {
          if (this.countdown > 0) {
            this.countdown -= 1; 
            console.log("Counting down:", this.countdown);
          } else {
            clearInterval(this.timer);
          }
        }, 1000); 
      } 
    },



    roundOver() {
      this.countdown = 0;
      clearInterval(this.timer);
      this.gameStart = false;
      var finishedInTime = this.colorBoxes.every(box => box.matched === true);
      if (finishedInTime) {
        this.score++;
        if(this.score > 0){
        this.countdown = 20 - (this.score * 2);
        }else{
        this.countdown = 18
        }
        this.colorBoxes = [];
        this.colorWords = [];
        this.selectedWords = [];
        finishedInTime = false;
      } else {
        this.score = 0;
        this.countdown = 20;
        this.colorBoxes = [];
        this.colorWords = [];
        this.selectedWords = [];
      }
    }


  },

  watch: {
    countdown(newValue) {
      if (newValue === 0) {
        this.roundOver();
      }
    },
  },

  mounted() {
    this.showModal = true;
  }
}

</script>

<style>

@import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;600;700&display=swap');


.app {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  font-family: 'Open Sans', sans-serif;
}

.timer {
  position: absolute;
  top: 50px; 
  font-size: 48px; 
}

.start-button {
  margin-top: 20px;
  width: 300px;
  height: 75px;
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  z-index: 1;
  text-align: center;
  font-size: 38px; 
  line-height: 70px; 
}

.start-button:hover {
  background-color: #0056b3; 
}

.header {
  position: absolute;
  top: 20px;
  left: 20px;
  font-size: 48px; 
  animation: rainbowAnimation 20s linear infinite; 
}

@keyframes rainbowAnimation {
  0% { color: violet; }
  10% { color: indigo; }
  20% { color: blue; }
  30% { color: green; }
  40% { color: yellow; }
  50% { color: orange; }
  60% { color: red; }
  70% { color: violet; }
  80% { color: indigo; }
  90% { color: blue; }
  100% { color: green; }
}

.background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: linear-gradient(-45deg, transparent 75%, rgba(169, 169, 169, 0.5) 25%);
  background-size: 100% 200%;
  animation: moveStripes 10s linear infinite;
}

.straighten {
  display: flex;
  align-items: center;
  justify-content: center; 
  width: 80%;
  position: relative;
  z-index: 1;
}

.boxes {
  display: flex;
  align-items: center; 
  margin-top: 140px; 
  margin-bottom: 30px;
}


.box {
  width: 100px;
  height: 100px;
  margin-right: 10px; 
  border-radius: 10px; 
}

.color-words {
  display: flex;
  flex-wrap: wrap; 
  justify-content: center; 
  margin-top: 50px; 
}

.word {
  font-size: 30px;
  margin-right: 30px;
  margin-bottom: 20px; 
  cursor: pointer;
  transition: box-shadow 0.3s ease; 
}

.word:hover {
  box-shadow: 
    0 0 10px 0 rgba(0, 0, 0, 0.5), 
    0 0 20px 0 #ff9eb5, 
    0 0 30px 0 #ffd3b6, 
    0 0 40px 0 #fff3b7, /* Light yellow shadow */
    0 0 50px 0 #c4f2a6, /* Light green shadow */
    0 0 60px 0 #a6f2fc, /* Light blue shadow */
    0 0 70px 0 #c9baf3, /* Light purple shadow */
    0 0 80px 0 #f9a1e6; /* Light magenta shadow */
  border-radius: 5px; /* Rounded corners for the shadow */
}

.fade-out {
  animation: fadeOut 0.3s ease forwards;
}

.score {
  position: fixed;
  top: 85px;
  right: 30px;
  font-size: 24px; 
  color: black; 
  z-index: 1;
}

@keyframes fadeOut {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
    display: none;
  }
}
</style>
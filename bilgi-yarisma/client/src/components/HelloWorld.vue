<template>
 <div class="container">
  <el-row>
    <!-- oyuna başlama butonu -->
    <el-button class="btn" v-if="!isStarted" @click="startGame" type="primary">Başla</el-button>
    <div v-else>
      <div class="question">
      <div v-for="(question, index) in questions" :key="index">
        <div v-if="index === currentQuestionNumber">
        <p>{{ (currentQuestionNumber+1) + '. ' +  question.q}}</p>
        <el-radio-group v-model="answers[index]">
          <div v-if="answerQuestion">
            <el-radio v-bind:class="{'green blink': isChecked && currentCorrectAnswer === currentChics[0].label }" :label="currentChics[0].label">{{currentChics[0].name}}</el-radio>
            <el-radio v-bind:class="{'green blink': isChecked && currentCorrectAnswer === currentChics[1].label }" :label="currentChics[1].label">{{currentChics[1].name}}</el-radio>
            <el-radio v-bind:class="{'green blink': isChecked && currentCorrectAnswer === currentChics[2].label }" :label="currentChics[2].label">{{currentChics[2].name}}</el-radio>
            <el-radio v-bind:class="{'green blink': isChecked && currentCorrectAnswer === currentChics[3].label }" :label="currentChics[3].label">{{currentChics[3].name}}</el-radio>
          </div>
          <el-button class="btn" v-if="control()" @click="answerQuestion" type="primary">Eminim</el-button>
        </el-radio-group>
        </div>
      </div>
      </div>
      <!-- yalnızca 10 soru sorulacağını belirtir. 10.sorudan sonra bitiyor -->
       <p v-if="currentQuestionNumber === questionCount">hepsini çözdünüz</p>
       <!-- oyunu tekrar oyna -->
       <el-button v-if="currentQuestionNumber === questionCount" @click="againGame" type="primary">Tekrar Çöz</el-button>
    </div>
  </el-row>
</div>
      
</template>

<script>
import axios from 'axios'
export default {
 data(){
   return {
     questions: [],
     answers: [null,null,null,null,null,null,null,null,null,null],
     trueAnswers: ["a","a","a","a","a","a","a","a","a","a"],
     currentQuestionNumber: 0,
     isStarted: false,
     questionCount:10,
     isChecked: false,
     timeOutDelay: 1000
   }
 },
  created(){
    this.getQuestions();
  },

  methods:{
    startGame(){
      this.isStarted = true;
    },
    answerQuestion(){
      if(this.currentAnswer){
        if(this.currentAnswer === this.currentCorrectAnswer){
            this.isChecked = true;
            this.playSound();
            setTimeout(() => {
              this.isChecked = false;
              this.currentQuestionNumber++;
            }, this.timeOutDelay);
         }
          else {
           alert('Yanlış cevap, oyun bitti');
           this.restartGame();
        }
      }
    },
    restartGame(){
      this.isStarted = false;
      this.currentQuestionNumber = 0;
      this.answers = [null,null,null,null,null,null,null,null,null,null];
      this.getQuestions();
    },
    againGame(){
      this.restartGame();
      this.isStarted = true;
    },
  
    async getQuestions(){
        const soru = await axios.get(`http://www.mustafacanpalaz.com/millionaireAPI/?limit=${this.questionCount}`);    
        this.questions = soru.data;
    },
    shuffle(arr) {
       return arr.sort((a, b) => Math.random() > .5 ? -1 : 1);
    },
    control(){
      return this.currentAnswer && !this.isChecked;
    },
    // doğru cevap ses efekti
    playSound(){
      const audio = new Audio ('https://www.myinstants.com/media/sounds/correct-answer-sound-effect-19.mp3');
      audio.play();
    }
  },
  computed: {
    currentAnswer() {
      return this.answers[this.currentQuestionNumber];
    },
    currentCorrectAnswer() {
      return this.trueAnswers[this.currentQuestionNumber];
    },
    currentQuestion() {
      return this.questions[this.currentQuestionNumber];
    },
    // datadan soruların şıklarını çektik
    currentChics() {
      const chic = [
        { name:this.currentQuestion.a1, label:"a"},
        { name:this.currentQuestion.a2, label:"b"},
        { name:this.currentQuestion.a3, label:"c"},
        { name:this.currentQuestion.a4, label:"d"},
      ];
      return this.shuffle(chic);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.container{
  width: 50%;
  padding-right: 25px;
  padding-left: 25px;
  padding-top: 15%;
  margin-right: auto;
  margin-left: auto;
  text-align: center;

}
.green {
  background-color: rgb(181, 236, 181);
  border-radius: 20%;
}
.btn{
  margin: 20px;
}
@keyframes blink {
  50% {
    opacity: 0.0;
  }
}
@-webkit-keyframes blink {
  50% {
    opacity: 0.0;
  }
}
.blink {
  animation: blink .4s step-start 0s infinite;
  -webkit-animation: blink .4s step-start 0s infinite;
}

.question{
  padding: 50px;
}
</style>

<script>

import AOS from 'aos';
import 'aos/dist/aos.css';
import axios from 'axios';

export default {
    name: 'GamePage',
  data() {
    return {
      QuestionAndAnswers: [],
      activeQuestionIndex: 0,
      answerIsHover: -1,
      showConfirm: false,
      selectedAnswer: '',
      yourAnswer: '',
      solutionIsVisible: true,
      solution: '',
      totalPoints: 0,

      gameOver: false,
      difficulty: this.$route.query.difficulty,


    }
  },

  methods: {
    generateQuests() {
      axios.get('https://opentdb.com/api.php?amount=10&difficulty=' + this.difficulty).then((response) => {
        console.log(response.data.results);

        response.data.results.forEach(element => {
          this.addNewQuest(element);
        });
                // {
        //   question: "",
        //   answers: [
        //     { text: "", correct:  },
        //     { text: "", correct:  },
        //     { text: "", correct:  },
        //     { text: "", correct:  }
        //   ]
        // }
      });
    },

    addNewQuest(questionApi) {
        const q = {
          question: null,
          answers: [],
        };
        
        this.QuestionAndAnswers.push(q);
        
        q.question = questionApi.question;//nome domanda da questionApi

        console.log(q.question)

        q.answers.push({
          text: questionApi.correct_answer,
          correct: true,
          rnd: Math.random()
        });
        questionApi.incorrect_answers.forEach(answer => {//verificare campo da cui recuperare elenco risposte
          q.answers.push( {
            text: answer,//testo risposta da elenco risposte questionAPI
            correct: false,//flag correttezza risposta da elenco risposte questionAPI
            rnd: Math.random()
          })
        });
        
        q.answers = q.answers.sort((a, b) => a.rnd - b.rnd);
    },

    activeAnswer(index) {
        this.answerIsHover = index;
    },

    selectAnswer(index) {
        this.showConfirm = true;
        this.selectedAnswer = index;
    },

    checkAnswer() {
        this.solutionIsVisible = false;
        this.yourAnswer = this.selectedAnswer;  
        this.yourAnswer = this.QuestionAndAnswers[this.activeQuestionIndex].answers[this.yourAnswer];


        if(this.yourAnswer.correct == false) {
            this.solution = 'La risposta è sbagliata'
            document.querySelector('.definitive').classList.add('failed');

        } else {
            this.solution = 'La risposta è corretta'
            document.querySelector('.definitive').classList.add('correct');
            this.totalPoints += 25;
        }

        this.solutionIsVisible = true;

        setTimeout(() => {
          this.activeQuestionIndex++;
          this.showConfirm = false;
          this.solutionIsVisible = false;
          this.solution = '';
          this.selectedAnswer = 0;
          document.querySelector('.definitive').classList.remove('failed');
          document.querySelector('.definitive').classList.remove('correct');
          if(this.activeQuestionIndex >= this.QuestionAndAnswers.length){
            this.gameOver = true;
            this.activeQuestionIndex = false;
          }
          
        }, '3000');


    }
  },

  mounted() {
    AOS.init();
    this.generateQuests();
  }
}

</script>

<template>    
    
    <main>

      <div  v-if="this.gameOver" class="container">
        <p id="game-over">The game is over! your total points are: {{ this.totalPoints }} pt</p>
        <router-link class="nav-link" exact-active-class="active" :to="{ name: 'home'}"><button>Go back to menù</button></router-link>
      </div>

      <div v-else class="container">
        <div id="play-container" v-if="this.QuestionAndAnswers.length > 0">
          <h1>Question number {{ this.activeQuestionIndex + 1 }} / {{ this.QuestionAndAnswers.length }}</h1>
  
            <div id="question" class="block">
                {{this.QuestionAndAnswers[activeQuestionIndex].question}}
            </div>
  
            <div id="answers-container">
                <div v-for="(answer, index) in QuestionAndAnswers[activeQuestionIndex].answers"
                  
                  :class="{'active': this.answerIsHover == index, 'definitive' : this.selectedAnswer == index}"  
                  @mouseover="activeAnswer(index)" 
                  @mouseleave="this.answerIsHover = -2" 
                  @click="selectAnswer(index)"
                  class="answer block">{{ answer.text }}
                </div>
            </div>
      
  
          </div>
  
          <div id="confirm-box" v-show="this.showConfirm">
              <p>Is it your definitive answer?</p>
              <button @click="checkAnswer()">Sì</button>
          </div>
  
          <h3>{{ this.solution }}</h3>
  
          <p id="points">Points: {{ this.totalPoints }} pt</p>

        </div>



    </main>


</template>

<style lang='scss' scoped>

    main {
        color: white;
        background-color: #11093a;
        position: relative;

        #points {
          position: absolute;
          top: 5px;
          right: 100px;
        }

        .container {
            margin: 0 auto;
            padding-top: 50px;
            width: 50%;

            display: flex;
            flex-flow: column;
            align-items: center;

            #play-container {
              display: flex;
              align-items: center;
              flex-flow: column;
            }

            .block {
                height: 50px;
                width: 400px;
                border: 2px solid white;
                border-radius: 15px;
                padding: 10px;

                text-align: center;

                display: flex;
                justify-content: center;
                align-items: center;

                &.active {
                    background-color: #ffffff18;
                    cursor: pointer;
                }

                &.definitive {
                    background-color: #ffffff54;
                }

                &.failed {
                  background-color: rgba(255, 0, 0, 0.281);
                }

                &.correct {
                  background-color: rgba(124, 250, 21, 0.281);

                }


                &#question {
                    display: flex;
                    justify-content: center;
                    align-items: center;

                    width: 70%;
                    margin-bottom: 50px;
                }

            }


            #answers-container {
                display: flex;
                flex-flow: row wrap;
                justify-content: center;
                gap: 1em 3em;

                width: 100%;
                height: 100%;


            }


        }

        #confirm-box {
            margin: 0 auto;
            max-width: 400px;

            display: flex;
            gap: 50px;

            margin-top: 50px;

            button {
                width: 80px;
                border-radius: 20px;

                border: none;
                background-color: #17198a;
                color: white;

                &:hover {
                    background-color: #2e2f8a;
                    cursor: pointer;
                }
            }
        }

        h3 {
            text-align: center;
        }
    }
</style>

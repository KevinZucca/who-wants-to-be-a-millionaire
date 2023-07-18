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
      towerIsVisible: false,
      activeTowerIndex: 14,

      towerLevels: [
        '$1 MILLION',
        '$500.000',
         '$250.000',
         '$125.000',
         '$64.000',
         '$32.000',
         '$16.000',
         '$8.000',
         '$4.000',
         '$2.000',
         '$1.000',
         '$500',
         '$300',
         '$200',
         '$100'
      ],

      modalIsVisible: false,
      correctAnswer: '',

    }
  },

  methods: {
    generateQuests() {
      axios.get('https://opentdb.com/api.php?amount=15&difficulty=' + this.difficulty).then((response) => {

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
            this.solution = 'The answer is wrong';
            document.querySelector('.definitive').classList.add('failed');
            this.showConfirm = false;

            setTimeout(() => {
              this.towerIsVisible = true;
              setTimeout(() => {
                this.towerIsVisible = false;
              }, '4000');
            }, '3000');
        } else {
            this.solution = 'Correct answer!'
            document.querySelector('.definitive').classList.add('correct');
            this.totalPoints += 25;
            this.showConfirm = false;

            setTimeout(() => {
              this.towerIsVisible = true;
              setTimeout(() => {
                this.activeTowerIndex--;
              }, '1000');
              setTimeout(() => {
                this.towerIsVisible = false;
              }, '4000');
            }, '3000');
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


    },

  },

  mounted() {
    AOS.init();
    this.generateQuests();
  },

}

</script>

<template>    
    
    <main>

      <div  v-if="this.gameOver" class="container">
        <p id="game-over">The game is over! You won</p>
        <p id="money">{{ this.towerLevels[this.activeTowerIndex] }} </p>
        <router-link class="nav-link" exact-active-class="active" :to="{ name: 'home'}"><button id="menu-back">Go back to menù</button></router-link>
      </div>

      <div v-else class="container" data-aos="fade-right" data-aos-duration="1200">
        <div id="play-container" v-if="this.QuestionAndAnswers.length > 0">
          <h1>Question number {{ this.activeQuestionIndex + 1 }} / {{ this.QuestionAndAnswers.length }}</h1>
  
            <p id="question" class="block" v-html="this.QuestionAndAnswers[activeQuestionIndex].question">
                
            </p>
  
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
              <button @click="checkAnswer()">Yes</button>
          </div>
  
          <h3>{{ this.solution }}</h3>
  
        </div>


        <div id="tower" v-if="this.towerIsVisible">
          <table>
              <tr v-for="(level, index) in this.towerLevels">
                <th :class="index == this.activeTowerIndex ? 'active' : ''">{{level}}</th>
              </tr>
          </table>
        </div>


        <button @click="this.modalIsVisible = !this.modalIsVisible" type="button" class="btn btn-primary modal-button" data-toggle="modal" data-target="#exampleModal">
            Go back to menù
        </button>

        <div v-if="this.modalIsVisible == true" id="modal">
            <strong>Are you sure you want to quit the current game?</strong>

            <div id="selections">
                <button id="keep-play" @click="this.modalIsVisible = false">No, i keep play</button>
                
                <router-link class="nav-link" exact-active-class="active" :to="{ name: 'home'}">
                    <button @click="this.modalIsVisible = false" id="stop-play">Yes, take me out</button>
                </router-link>
            </div>
        </div>

    </main>


</template>

<style lang='scss' scoped>

    main {
        height: calc(100vh - 300px);
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

            #game-over {
              font-size: 3em;
            }

            #money {
              font-size: 4em;
              color: #ff9900;
              margin-top: 0;
            }

            #menu-back {
              width: 300px;
              height: 100px;
              border-radius: 20px;

                border: none;
                background-color: #17198a;
                color: white;

                font-size: 1.5em;

                &:hover {
                    background-color: #2e2f8a;
                    cursor: pointer;
                }
            }

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
            margin-top: 70px;
        }

        #tower {
          display: flex;
          justify-content: center;
          align-items: center;

          position: fixed;
          top: 50%;
          left: 50%;
          transform: translate(-50%, -50%);

          width: 100%;
          height: 100%;

          background-image: url('https://cdn.wallpapersafari.com/79/79/4p27kj.jpg');
          background-size: cover;

          z-index: 4;

          table {
            width: 20%;
            height: 60%;
            font-size: 2.5em;

            padding: 20px;
            background-color: #ffffff11;
            th {
              color: #ff9900;
              display: flex;
              align-items: center;
              justify-content: center;

              &.active {
                background-color: #090b8c;
                border: 1px solid rgba(255, 255, 255, 0.436);
              }
            }
          }
        }

        .modal-button {
        position: absolute;
        top: 10px;
        left: 10px;

        width: 150px;
        height: 70px;

        font-size: 1.2em;
        border-radius: 20px;

        background-color: rgba(78, 78, 209, 0.434);
        color: white;
        border: none;

        &:hover {
          cursor: pointer;
        }
    }

    #modal {
        width: 50%;
        height: 50%;
        position: fixed;
        top: 30%;
        left: 50%;
        transform: translate(-50%);

        display: flex;
        justify-content: center;
        align-items: center;
        flex-flow: column;

        background-color: #000000;
        color: rgba(255, 255, 255, 0.74);
        opacity: 90%;

        z-index: 1;

        strong {
          font-size: 1.3em;
          text-align: center;
        }

        #selections {
            display: flex;
            gap: 2em;
            margin-top: 20px;

            button {
                font-size: 1.2em;
                padding: 0.5em;

                color: white;
                border: none;
                border-radius: 10px;
                transition: all .2s ease-in-out;

                &#stop-play {
                    background-color: rgb(106, 200, 106);
                }

                &#keep-play {
                    background-color: rgb(190, 76, 76);
                }

                &:hover {
                    cursor: pointer;
                    scale: 1.1;
                }
            }
        }
      }
    }
</style>

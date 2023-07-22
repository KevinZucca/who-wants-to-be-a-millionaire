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

      helps: [
        {
          src: 'https://th.bing.com/th/id/R.59c71e3156b3e17c4850983850507a9a?rik=AH2c%2bhcM1r%2bS8A&riu=http%3a%2f%2fvignette4.wikia.nocookie.net%2fmillionaire%2fimages%2f7%2f73%2fClassic5050.png%2frevision%2flatest%3fcb%3d20160407180209&ehk=LfaxEmizV8BEKQWwq6Uh1N%2fTzDgBwimjZOi1aGkZ6I8%3d&risl=&pid=ImgRaw&r=0&sres=1&sresct=1',
          name: 'Fifty-fifty'
        },
        {
          src: 'https://th.bing.com/th/id/R.8af91c80f9bf9861e68d604669f82ac4?rik=uwsAcfaxkRr2mQ&riu=http%3a%2f%2fvignette3.wikia.nocookie.net%2fmillionaire%2fimages%2f8%2f88%2fClassicPAF.png%2frevision%2flatest%3fcb%3d20160407180816&ehk=G1q%2bpbZqMznbdec%2fvlXbJHX6EvW2esIqORYjha7fx0w%3d&risl=&pid=ImgRaw&r=0&sres=1&sresct=1',
          name: 'Call a Super-Smart friend'
        },
        {
          src: 'https://th.bing.com/th/id/R.b187224b9588d2d1938ff725ff760309?rik=JMSt5FD3FqTIdQ&riu=http%3a%2f%2fvignette2.wikia.nocookie.net%2fmillionaire%2fimages%2fc%2fc6%2fClassicATA.png%2frevision%2flatest%3fcb%3d20160407180412&ehk=BVZdfFYkl4DVM8mzVyC0gGJnHpCAHgYrOBdkvrbDF5s%3d&risl=&pid=ImgRaw&r=0&sres=1&sresct=1',
          name: 'Ask the audience'
        }
      ],

      confirmHelp: false,
      helpIndex: 0,
      helpUsed: [],

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
          rnd: Math.random(),
          helpUsed: false
        });
        questionApi.incorrect_answers.forEach(answer => {//verificare campo da cui recuperare elenco risposte
          q.answers.push( {
            text: answer,//testo risposta da elenco risposte questionAPI
            correct: false,//flag correttezza risposta da elenco risposte questionAPI
            rnd: Math.random(),
            helpUsed: false
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
        const answersContainer = document.querySelector('#answers-container');


        if(this.yourAnswer.correct == false) {
            this.solution = 'The answer is wrong';
            document.querySelector('.definitive').classList.add('failed');
            this.showConfirm = false;
            answersContainer.style.pointerEvents = ('none');

        } else {
            this.solution = 'Correct answer!'
            document.querySelector('.definitive').classList.add('correct');
            this.totalPoints += 25;
            this.showConfirm = false;
            answersContainer.style.pointerEvents = ('none');

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
          answersContainer.style.pointerEvents = ('auto');
          if(this.activeQuestionIndex >= this.QuestionAndAnswers.length){
            this.gameOver = true;
            this.activeQuestionIndex = false;
          }
          
        }, '3000');


    },

    selectHelp(index){
      this.confirmHelp = true;
      this.helpIndex = index;
      let allAnswers = document.querySelectorAll('.answer');
      let answers = this.QuestionAndAnswers[this.activeQuestionIndex].answers;
      const helpImg = document.querySelectorAll('.help-img')[index];
      let hiddenAnswers = [];
      let wrongAnswers = [];

      if(index == 0 && answers.length > 2) {
        helpImg.classList.add('used');

        for(let i = 0; i < answers.length; i++){
          if(answers[i].correct != true){
            wrongAnswers.push(i)
          }
        }

        while (hiddenAnswers.length < 2) {
          let randomIndex = Math.floor(Math.random() * wrongAnswers.length);
          if (!hiddenAnswers.includes(randomIndex)) {
            hiddenAnswers.push(randomIndex);
          }
        }

        hiddenAnswers.forEach((index) => {
          allAnswers[wrongAnswers[index]].style.display = 'none';
        });

        setTimeout(() => {
          hiddenAnswers.forEach((index) => {
          allAnswers[wrongAnswers[index]].style.display = 'block';
        });
        }, 4000);

      } else {
        alert("You can't use the fifty fifty on a true false question")
      }

    }

  },

  mounted() {
    AOS.init();
    this.generateQuests();

  },

}



</script>

<template>    
    
    <main>

      <div class="row">
        <div class="col-4">
          <button @click="this.modalIsVisible = !this.modalIsVisible" type="button" class="btn btn-primary modal-button" data-toggle="modal" data-target="#exampleModal">
            Go back to menù
          </button>
        </div>
        <!-- <div class="col-4" id="help-confirm" v-show="this.confirmHelp == true">
         <h6>
          Are you sure you want to use the "{{ this.helps[this.helpIndex].name }}" help ?
         </h6>
         <button>Yes</button>
         <button @click="this.confirmHelp = false">No</button>
        </div> -->
        <div class="col-4" id="helps-container">
          <div class="single-help" v-for="(help, index) in this.helps">
            <img class="help-img" @click="selectHelp(index)" :src="help.src" alt="help-img">
            <p>{{help.name}}</p>
          </div>
        </div>
      </div>

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
                  v-html="answer.text"
                  :class="{'active': this.answerIsHover == index, 'definitive' : this.selectedAnswer == index}"  
                  @mouseover="activeAnswer(index)" 
                  @mouseleave="this.answerIsHover = -2" 
                  @click="selectAnswer(index)"
                  class="answer block">
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
            height: 100%;
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

        .row {
          width: 100%;
          display: flex;
          justify-content: space-between;

          .modal-button {
          margin-top: 0px;
          margin-left: 20px;
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

          .col-4 {
            display: flex;
            align-items: center;

            &#helps-container {
              display: flex;
              justify-content: flex-end;
  
              img {
                  margin-top: 20px;
                  width: 150px;
                  height: 70px;
                  object-fit: contain;

                  filter: opacity(80%);
                  transition: all .2s ease-in;

                  &:hover {
                    scale: 1.1;
                    cursor: pointer;
                    filter: opacity(110%);
                  }

                  &.used {
                    filter: saturate(0);
                    pointer-events: none;

                    &:hover {
                      scale: 1;
                      cursor: default;
                    }
                  }
              }
    
            }
            .single-help {
              display: flex;
              flex-flow: column;
              align-items: center;
              gap: 10px;
  
              &#help-confirm {
                flex-grow: 1;
                justify-content: center;
                gap: 20px;
  
  
                button {
                  width: 50px;
                  padding: 5px;
  
                  border-radius: 10px;
                  border: none;
  
                  color: white;
                  background-color: #191b8a;
  
                  &:hover {
                    background-color: #3d3e8d;
                  }
                }
              }

            }

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


  //   @media only screen and (max-width: 990px) {
   
  //     .container {
  //           padding-top: 20px;

  //           #menu-back {
  //             width: 100px;
  //             height: 50px;
  //             border-radius: 10px;
  //             font-size: 1.2em;

  //               &:hover {
  //                   background-color: #2e2f8a;
  //                   cursor: pointer;
  //               }
  //           }

  //           .block {
  //               height: 0px;
  //               width: 400px;
  //               border: 2px solid white;
  //               border-radius: 15px;
  //               padding: 10px;

  //               &#question {
  //                   display: flex;
  //                   justify-content: center;
  //                   align-items: center;

  //                   width: 70%;
  //                   margin-bottom: 50px;
  //               }

  //           }


  //           #answers-container {
  //               display: flex;
  //               flex-flow: row wrap;
  //               justify-content: center;
  //               gap: 20em 3em;

  //               width: 100%;
  //               height: 100%;

  //           }


  //       }
  // }
</style>

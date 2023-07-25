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
      hiddenAnswers: [],
      friendHelpIsVisible: false,
      publicOpinions: [],
      majorPercentageAnswer: '',
      otherPercentages: [],
      graphIsVisible: false,

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
        console.log(this.QuestionAndAnswers[this.activeQuestionIndex])
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
          this.graphIsVisible = false;
          
          let allAnswers = document.querySelectorAll('.answer');
          allAnswers.forEach((element) => {
            element.style.display = 'block';
          });

          this.friendHelpIsVisible = false;
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
      let wrongAnswers = [];

      if(index == 0 && answers.length > 2) {
        helpImg.classList.add('used');

        for(let i = 0; i < answers.length; i++){
          if(answers[i].correct != true){
            wrongAnswers.push(i)
          }
        }

        while (this.hiddenAnswers.length < 2) {
          let randomIndex = Math.floor(Math.random() * wrongAnswers.length);
          if (!this.hiddenAnswers.includes(randomIndex)) {
            this.hiddenAnswers.push(randomIndex);
          }
        }

        this.hiddenAnswers.forEach((index) => {
          allAnswers[wrongAnswers[index]].style.display = 'none';
        });

      } else if (index == 0 && answers.length <= 2) {
        alert("You can't use the fifty fifty on a true false question")

      } else if (index == 1) {

        helpImg.classList.add('used');
        for(let i = 0; i < answers.length; i++){
          if(answers[i].correct == true){
            this.correctAnswer = i
          }
        }
        this.friendHelpIsVisible = true;
        
      } else if(index == 2) {
        helpImg.classList.add('used');
        this.graphIsVisible = true;
        let correctPublicAnswerPercentage;
        let difference;
        let random1; 
        let random2;
        let random3;

        
        if(this.difficulty == 'easy') {
          correctPublicAnswerPercentage = this.getRandomPercentage(70, 95);
          this.majorPercentageAnswer = correctPublicAnswerPercentage;
          difference = 100 - correctPublicAnswerPercentage;
          random1 = this.getRandomPercentage(1, difference - 2);
          random2 = this.getRandomPercentage(1, difference - random1 - 1);
          random3 = difference - random1 - random2;
          this.publicOpinions.push(random1, random2, random3);

          for (let i = 0; i < this.QuestionAndAnswers[this.activeQuestionIndex].answers.length; i++) {
            const graph = this.QuestionAndAnswers[this.activeQuestionIndex].answers[i];
            if (graph.correct) {
              graph.publicPercentage = this.majorPercentageAnswer;
            } else {
              graph.publicPercentage = this.publicOpinions.shift();
          }
        }
        } else if(this.difficulty == 'medium') {
          correctPublicAnswerPercentage = this.getRandomPercentage(40, 70);
          difference = 100 - correctPublicAnswerPercentage;
          random1 = this.getRandomPercentage(1, difference - 2);
          random2 = this.getRandomPercentage(1, difference - random1 - 1);
          random3 = difference - random1 - random2;
          this.publicOpinions.push(random1, random2, random3);

          for (let i = 0; i < this.QuestionAndAnswers[this.activeQuestionIndex].answers.length; i++) {
            const graph = this.QuestionAndAnswers[this.activeQuestionIndex].answers[i];
            if (graph.correct) {
              graph.publicPercentage = correctPublicAnswerPercentage;
            } else {
              graph.publicPercentage = this.publicOpinions.shift();
          }
        }
        } else if(this.difficulty == 'hard') {
          correctPublicAnswerPercentage = this.getRandomPercentage(20, 45);
          difference = 100 - correctPublicAnswerPercentage;
          random1 = this.getRandomPercentage(1, difference - 2);
          random2 = this.getRandomPercentage(1, difference - random1 - 1);
          random3 = difference - random1 - random2;
          this.publicOpinions.push(random1, random2, random3);

          for (let i = 0; i < this.QuestionAndAnswers[this.activeQuestionIndex].answers.length; i++) {
            const graph = this.QuestionAndAnswers[this.activeQuestionIndex].answers[i];
            if (graph.correct) {
              graph.publicPercentage = correctPublicAnswerPercentage;
            } else {
              graph.publicPercentage = this.publicOpinions.shift();
          }
        }
        }
      }


    },

    getRandomPercentage(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min;
    },

    viewHelps() {
      const helps = document.querySelectorAll('.single-help');
      helps.forEach(help => {
        if (help.style.display == 'block') {
          help.style.display = 'none';
        } else {
          help.style.display = 'block';
        }
      });
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
          <button @click="this.modalIsVisible = !this.modalIsVisible" type="button" class="btn btn-primary modal-button lg-200" data-toggle="modal" data-target="#exampleModal">
            <p id="menu-back" class="d-sm-none d-lg-block">Go back to menù</p>
            <p id="left-arrow " class="fs-2 d-lg-none d-sm-block">&leftarrow;</p>
          </button>
        </div>

        <div class="col-4" id="helps-container">
          <div class="single-help" v-for="(help, index) in this.helps">
            <img class="help-img" @click="selectHelp(index)" :src="help.src" alt="help-img">
            <p class="d-sm-none d-xl-block ">{{help.name}}</p>
          </div>

          <button @click="viewHelps()" id="helps-button" type="button" class="btn btn-primary modal-button lg-200" data-toggle="modal" data-target="#exampleModal">Helps</button>
        </div>
      </div>

      <div  v-if="this.gameOver" class="container game-over-container">
        <p id="game-over">The game is over! You won</p>
        <p id="money">{{ this.towerLevels[this.activeTowerIndex] }} </p>
        <router-link class="nav-link" exact-active-class="active" :to="{ name: 'home'}"><button id="menu-back">Go back to menù</button></router-link>
      </div>

      <div v-else class="container main-container" data-aos="fade-right" data-aos-duration="1200">
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

             <!-- FRIEND HELP -->
          <div id="friend-container" v-if="this.friendHelpIsVisible == true" data-aos="fade-left">
            <span>
              <lord-icon
                src="https://cdn.lordicon.com/imamsnbq.json"
                trigger="loop"
                colors="primary:#121331,secondary:#121331"
                style="width:80px;height:80px">
              </lord-icon>
            </span>
            <i>I'm absolutely sure about the answer number {{ this.correctAnswer + 1 }} !</i>
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

        <!-- GRAPH HELP -->
        <div id="graph-container" data-aos="fade-right" v-if="this.graphIsVisible == true">
          <span @click="this.graphIsVisible = false">X</span>
          <div class="container">
            <div class="single-graph" v-for="graph in this.QuestionAndAnswers[this.activeQuestionIndex].answers" data-aos="fade-in" data-aos-duration="2000">
              <p>{{ graph.publicPercentage }}%</p>
              <div class="graph" :style="{height: graph.publicPercentage + '%'}"></div>
              <h6 v-html="graph.text"></h6>
            </div>

          </div>
        </div>

    </main>


</template>

<style lang='scss' scoped>

    main {
        caret-color: transparent;
        min-height: 800px;
        color: white;
        background-color: #11093a;
        position: relative;

        #points {
          position: absolute;
          top: 5px;
          right: 100px;
        }

        .container.game-over-container {
          display: flex;
          flex-flow: column;
          justify-content: center;
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
        }

        .container.main-container {
            margin: 0 auto;
            height: 100%;
            padding-top: 0px;
            width: 50%;

            display: flex;
            flex-flow: column;
            align-items: center;


            #play-container {
              display: flex;
              align-items: center;
              flex-flow: column;
            }

            #friend-container {
              max-width: 70%;
              height: 100px;
              margin-top: 80px;

              display: flex;
              justify-content: center;
              align-items: center;

              background-color: rgba(255, 255, 255, 0.781);
              color: black;

              i {
                color: #2f318e;
                font-weight: bold;
                font-size: 1em;
                padding: 6px;
              }
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
                    min-height: 80px;
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
          background-position: center;
          background-size: cover;

          z-index: 4;

          table {
            width: 40%;
            height: 60%;
            font-size: 2.5em;

            padding: 20px;
            background-color: #ffffff11;
            th {
              height: 50px;
              color: #ff9900;
              display: flex;
              align-items: center;
              justify-content: center;

              &:first-child {
                font-size: .8em;
                overflow: hidden;
                white-space: nowrap;
                text-overflow: ellipsis;
                
              }

              &.active {
                background-color: #121496;
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

              #helps-button {
                display: none;
              }
  
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

              p {
                height: 30px;
                text-align: center;
              }
  
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

      #graph-container {
        display: flex;
        justify-content: center;
        align-items: center;

        position: absolute;
        top: 0;
        left: 0;
        width: 25%;
        padding: 10px;

        background-color: rgba(0, 0, 0, 0.913);
        height: 100%;
        width: 100%;

        z-index: 2;

        span {
          position: absolute;
          top: 5px;
          right: 5px;

          padding: 5px 8px;
          border-radius: 3px;

          background-color: rgba(255, 255, 255, 0.765);
          color: rgb(212, 75, 75);
          font-weight: bold;
        }

        .container {
          display: flex;
          justify-content: center;
          flex-flow: row;
          gap: 1em;

          width: 100%;
          height: 80%;
          padding: 50px;

          .single-graph {
            display: flex;
            flex-flow: column;
            gap: 10px;
            justify-content: flex-end;
            align-items: center;

            height: 100%;
            width: calc(100% / 4);

            p {
              height: 5%;
            }

            h6 {
              height: 20px;
              width: 100%;
              text-align: center;

              white-space: nowrap; /* Impedisce al testo di andare a capo */
              overflow: hidden;
              text-overflow: ellipsis;
            }
            
            .graph {
              width: 100%;
              background-color: blue;
            }
          }
        }
      }
    }



    // media queries


    @media only screen and (max-width: 575px) {
      main {
        .row {
          padding-top: 20px;
          &:first-child {
            flex-flow: row;
            padding-top: 10px;
          }
          .col-4 {
            height: 100%;

            button {
              width: 40px;
            }

            &#helps-container {
              flex-flow: column;
              align-items: flex-end;
              justify-content: flex-start;
              gap: 5px;

              padding-right: 0;

              #helps-button {
                display: block;
                margin: 10px 0;
                order: -1;
              }

              .single-help {
                background-color: rgba(0, 0, 0, 0.76);
                padding: 10px;
                z-index: 1;
                display: none;

                &:first-child {
                  border-radius: 30% 0 0 0;
                }

                }

              p {
                display:none;
              }
            }

            #menu-back {
              display: none;
            }
          }
        }

        .container.main-container {
          #play-container {
            h1 {
              font-size: .8em;
            } 

            #question {
              width: 100%;
            }
          }
        }

      }
    }

    @media only screen and (max-width: 991px) {
      main {
        height: 100vh;
        .row {
          height: 70px;
       
          .col-4 {
            button {
              width: 80px;
            }

            &#helps-container {              
              gap: 0;
              
              .single-help {      
                img {
                  width: 80px;
                }
              }

            }
          }
        }

         .container.game-over-container {
            #game-over {
              padding-top: 30px;
              font-size: 2em;
            }

            #money {
              font-size: 3em;
            }

            #menu-back {
              width: 200px;
              height: 80px;

              font-size: 1.2em;

              &:hover {
                  background-color: #2e2f8a;
                  cursor: pointer;
              }
            }
        }

        .container.main-container {
          padding-top: 30px;
          width: 70%;
            #play-container {
              width: 140%;

              .block {
                font-size: .9em;
                max-width: 95%;
              }
              h1 {
                font-size: 1.7em;
              }

              #question {
                width: 400px;
                max-width: 95%;
                min-height: 120px;
              }

              #answers-container {
                flex-flow: column;
                align-items: center;
              }
            }

         

          }

          #modal {
            width: 70%;
            height:40%;
            padding: 1em;

            strong {
              font-size: 1.1em;
            }

            #selections {
                display: flex;

                button {
                    font-size: .9em;
                }
            }
          }


        }

       
}


</style>

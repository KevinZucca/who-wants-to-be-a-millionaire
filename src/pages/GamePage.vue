<script>

export default {
    name: 'GamePage',
  data() {
    return {
        QuestionAndAnswers: [
        {
          question: "Qual è il nome scientifico dell'elemento chimico noto come 'piombo'?",
          answers: [
            { text: "Plb", correct: false },
            { text: "Pb", correct: true },
            { text: "Ld", correct: false },
            { text: "Po", correct: false }
          ]
        },
        {
          question: "Quale è il compositore della famosa opera 'Le quattro stagioni'?",
          answers: [
            { text: "Ludwig van Beethoven", correct: false },
            { text: "Wolfgang Amadeus Mozart", correct: false },
            { text: "Antonio Vivaldi", correct: true },
            { text: "Johann Sebastian Bach", correct: false }
          ]
        },
        {
          question: "Quale astronomo ha formulato la legge dei pianeti che porta il suo nome?",
          answers: [
            { text: "Galileo Galilei", correct: false },
            { text: "Nicolaus Copernicus", correct: false },
            { text: "Isaac Newton", correct: false },
            { text: "Johannes Kepler", correct: true }
          ]
        },
        {
          question: "Quale evento storico ha avuto luogo nella città di Versailles nel 1919?",
          answers: [
            { text: "Rivoluzione francese", correct: false },
            { text: "Proclamazione dell'indipendenza degli Stati Uniti", correct: false },
            { text: "Prima guerra mondiale", correct: false },
            { text: "Trattato di Versailles", correct: true }
          ]
        },
        {
          question: "Quale scienziato ha scoperto la teoria della relatività?",
          answers: [
            { text: "Isaac Newton", correct: false },
            { text: "Albert Einstein", correct: true },
            { text: "Niels Bohr", correct: false },
            { text: "Marie Curie", correct: false }
          ]
        }
      ],


      activeQuestionIndex: 0,
      answerIsHover: -1,
      showConfirm: false,
      selectedAnswer: '',
      yourAnswer: '',
      solutionIsVisible: true,
      solution: '',
      totalPoints: 0,

    }
  },

  methods: {
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
        }, '3000');
        
    },
  }
}

</script>

<template>    
    
    <main>
        <div class="container">

          <h1>Domanda numero {{ this.activeQuestionIndex + 1 }}</h1>

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
            <p>E' la tua risposta definitiva?</p>
            <button @click="checkAnswer()">Sì</button>
        </div>

        <h3>{{ this.solution }}</h3>

        <p id="points">Punteggio: {{ this.totalPoints }} pt</p>

    </main>


</template>

<style lang='scss' scoped>

    main {
        height: 600px;
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

            .block {
                height: 50px;
                width: 400px;
                border: 2px solid white;
                border-radius: 15px;

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

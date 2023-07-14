<script>

export default {
    name: 'GamePanel',
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
      selectedAnswer: false,
      yourAnswer: '',
      solutionIsVisible: true,
      solution: '',
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
        } else {
            this.solution = 'La risposta è corretta'
        }

        this.solutionIsVisible = true;
    }
  }
}

</script>

<template>
    <header>
        <div id="logo-container">
            <img src="https://boolean.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0b8e1102-7ff4-4870-a39e-ef625253af75%2Flogo.png?id=d1419994-97c4-4690-b349-03db5eada926&table=block&spaceId=4c49ff97-016b-4669-8de5-4479dd1a86e1&width=480&userId=&cache=v2" alt="logo-img">
        </div>
    </header>

    
    
    <main>
        <div class="container">

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

    </main>


</template>

<style lang='scss' scoped>

    header {
        height: 300px;
        background-color: #090b8c;

        display: flex;
        justify-content: center;
        align-items: center;
     
            #logo-container {
                margin: 0 auto;

                width: 250px;
                height: 250px;
                
                display: flex;
                justify-content: center;
                align-items: center;
    
                img {
                    width: 100%;
                    height: 100%;
                    object-fit: cover;
                }
            }

    }

    main {
        height: 600px;
        color: white;
        background-color: #11093a;

        .container {
            margin: 0 auto;
            padding-top: 50px;
            width: 50%;

            display: flex;
            flex-flow: column;
            align-items: center;

            .block {
                height: 50px;
                width: 350px;
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

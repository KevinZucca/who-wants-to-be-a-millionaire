<script>

import AOS from 'aos';
import 'aos/dist/aos.css';

export default {
    name: 'GamePage',
  data() {
    return {
      QuestionAndAnswers: [
  {
    question: "Qual è il più grande oceano del mondo?",
    answers: [
      { text: "Oceano Atlantico", correct: false },
      { text: "Oceano Indiano", correct: false },
      { text: "Oceano Pacifico", correct: true },
      { text: "Oceano Artico", correct: false }
    ]
  },
  {
    question: "Chi ha dipinto il quadro 'La notte stellata'?",
    answers: [
      { text: "Pablo Picasso", correct: false },
      { text: "Vincent van Gogh", correct: true },
      { text: "Leonardo da Vinci", correct: false },
      { text: "Claude Monet", correct: false }
    ]
  },
  {
    question: "Qual è l'elemento chimico con il simbolo 'Fe'?",
    answers: [
      { text: "Ferro", correct: true },
      { text: "Fluoro", correct: false },
      { text: "Fosforo", correct: false },
      { text: "Francio", correct: false }
    ]
  },
  {
    question: "In quale paese si trova la Grande Muraglia?",
    answers: [
      { text: "Giappone", correct: false },
      { text: "India", correct: false },
      { text: "Cina", correct: true },
      { text: "Italia", correct: false }
    ]
  },
  {
    question: "Chi ha scritto il romanzo '1984'?",
    answers: [
      { text: "George Orwell", correct: true },
      { text: "J.R.R. Tolkien", correct: false },
      { text: "F. Scott Fitzgerald", correct: false },
      { text: "Aldous Huxley", correct: false }
    ]
  },
  {
    question: "Qual è l'organo più grande del corpo umano?",
    answers: [
      { text: "Cervello", correct: false },
      { text: "Polmone", correct: false },
      { text: "Cuore", correct: false },
      { text: "Pelle", correct: true }
    ]
  },
  {
    question: "Quale pianeta del sistema solare ha il giorno più lungo?",
    answers: [
      { text: "Venere", correct: true },
      { text: "Marte", correct: false },
      { text: "Giove", correct: false },
      { text: "Saturno", correct: false }
    ]
  },
  {
    question: "Chi ha scritto il romanzo 'Il conte di Montecristo'?",
    answers: [
      { text: "Victor Hugo", correct: false },
      { text: "Alexandre Dumas", correct: true },
      { text: "Gustave Flaubert", correct: false },
      { text: "Honoré de Balzac", correct: false }
    ]
  },
  {
    question: "Qual è il più alto vulcano attivo sulla Terra?",
    answers: [
      { text: "Vesuvio", correct: false },
      { text: "Etna", correct: false },
      { text: "Kilauea", correct: true },
      { text: "Stromboli", correct: false }
    ]
  },
  {
    question: "In quale anno è stata fondata la Microsoft?",
    answers: [
      { text: "1975", correct: true },
      { text: "1985", correct: false },
      { text: "1995", correct: false },
      { text: "2005", correct: false }
    ]
  },
  {
    question: "Qual è la valuta ufficiale del Giappone?",
    answers: [
      { text: "Dollaro", correct: false },
      { text: "Yuan", correct: false },
      { text: "Euro", correct: false },
      { text: "Yen", correct: true }
    ]
  },
  {
    question: "Qual è il fiume più lungo del mondo?",
    answers: [
      { text: "Nilo", correct: true },
      { text: "Mississippi", correct: false },
      { text: "Amazzoni", correct: false },
      { text: "Danubio", correct: false }
    ]
  },
  {
    question: "Chi ha scritto il romanzo 'Guerra e pace'?",
    answers: [
      { text: "Leo Tolstoj", correct: true },
      { text: "Fyodor Dostoevsky", correct: false },
      { text: "Anton Chekhov", correct: false },
      { text: "Mikhail Bulgakov", correct: false }
    ]
  },
  {
    question: "Qual è la montagna più alta del mondo?",
    answers: [
      { text: "K2", correct: false },
      { text: "Everest", correct: true },
      { text: "Kilimanjaro", correct: false },
      { text: "Monte Bianco", correct: false }
    ]
  },
  {
    question: "Chi ha scritto il romanzo 'Don Chisciotte'?",
    answers: [
      { text: "Miguel de Cervantes", correct: true },
      { text: "Gabriel García Márquez", correct: false },
      { text: "Jorge Luis Borges", correct: false },
      { text: "Pablo Neruda", correct: false }
    ]
  },
  {
    question: "In quale paese si trova la Cappella Sistina?",
    answers: [
      { text: "Italia", correct: true },
      { text: "Francia", correct: false },
      { text: "Spagna", correct: false },
      { text: "Germania", correct: false }
    ]
  },
  {
    question: "Chi ha dipinto il quadro 'Il bacio'?",
    answers: [
      { text: "Auguste Rodin", correct: false },
      { text: "Gustav Klimt", correct: true },
      { text: "Salvador Dalí", correct: false },
      { text: "Leonardo da Vinci", correct: false }
    ]
  },
  {
    question: "Quale città è conosciuta come la 'Città eterna'?",
    answers: [
      { text: "Parigi", correct: false },
      { text: "Atene", correct: false },
      { text: "Roma", correct: true },
      { text: "Londra", correct: false }
    ]
  },
  {
    question: "Qual è l'elemento chimico con il simbolo 'Hg'?",
    answers: [
      { text: "Mercurio", correct: true },
      { text: "Idrogeno", correct: false },
      { text: "Argento", correct: false },
      { text: "Oro", correct: false }
    ]
  },
  {
    question: "Chi ha scritto il romanzo 'Orgoglio e pregiudizio'?",
    answers: [
      { text: "Emily Brontë", correct: false },
      { text: "Jane Austen", correct: true },
      { text: "Charlotte Brontë", correct: false },
      { text: "Virginia Woolf", correct: false }
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

      gameOver: false,

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
          if(this.activeQuestionIndex >= this.QuestionAndAnswers.length){
            this.gameOver = true;
            this.activeQuestionIndex = false;
          }
          
        }, '3000');


    },
  },

  mounted() {
    AOS.init();
  }
}

</script>

<template>    
    
    <main>

      <div  v-if="this.gameOver" class="container">
        <p id="game-over">Gioco terminato! il tuo punteggio totale è: {{ this.totalPoints }} pt</p>
        <router-link class="nav-link" exact-active-class="active" :to="{ name: 'home'}"><button>Torna al menù</button></router-link>
      </div>

      <div v-else class="container">

        <h1>Domanda numero {{ this.activeQuestionIndex + 1 }} / {{ this.QuestionAndAnswers.length }}</h1>

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

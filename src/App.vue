<script setup>
// import { RouterLink, RouterView } from "vue-router";
// import HelloWorld from "./components/HelloWorld.vue";
import { ref } from 'vue';

//Word to guess
let word = ref('science');

//Break word up into array
let wordArray = ref(word.value.split(''));

//Displayed word
let rawGuess = ref([]);

//Number of guesses
let guessCount = ref('12');

//Letter or word guessed by user
let guess = ref('');

//Feedback message
let message = ref('Guess a letter or the word');

//Make the displayed word an array of blanks
wordArray.value.map(() => {
  rawGuess.value.push('_');
});

//Then display array as string
let wordGuess = ref(rawGuess.value.join(" "));

//User's guessed letters & words are contained in these arrays
let guessedLetters = [];
let guessedWords = [];

let noMoreGuesses = ref(0);

function makeGuess(userGuess) {
  if(guessCount.value > 0) {
    if(userGuess.length == 1) {
      if(guessedLetters.includes(userGuess)) {
        message.value = "You've arleady guessed this letter, try again";
      }
      else {
        //Valid 1-letter guess
        if (wordArray.value.includes(userGuess)) {
          let count = 0;
          for(let i = 0; i < wordArray.value.length; i++){
            if(wordArray.value[i] == userGuess) {
              count++;
              rawGuess.value[i] = userGuess;
            }
          }
          if(rawGuess.value.includes('_')) {
            message.value = "The letter " + userGuess + " occurs " + count +  " time(s) in the word";
            wordGuess.value = rawGuess.value.join(" ");
          }
          else {
            message.value = "Correct! You win!";
            wordGuess.value = word.value;
            guessCount.value = 0;
          }
          
        }
        //Invalid 1-letter guess
        else {
          message.value = "Letter not in word, try again";
        }
        guessedLetters.push(userGuess);
        if(guessCount.value != 0) {
          guessCount.value--;
        }
        if(guessCount.value == 0) {
          noMoreGuesses.value = 1;
          message.value += "... oops, no more guesses, maybe next time?"
        }
      }
    }
    else if(userGuess.length == word.value.length) {
      if(guessedWords.includes(userGuess)) {
        message.value = "You've already guessed this word, try again"
      }
      else {
        if(userGuess == word.value) {
          message.value = "Correct! You win!";
          wordGuess.value = word.value;
          guessCount.value = 0;
          noMoreGuesses.value = 1;
        }
        else {
          message.value = "Incorrect word, try again"
          guessedWords.push(userGuess);
          guessCount.value--;
          if(guessCount.value == 0) {
            noMoreGuesses.value = 1;
            message.value += "... oops, no more guesses, maybe next time?"
          }
        }
      }
    }
    else {
      message.value = "Invalid guess, try again"
    }
  }
  else {
    message.value = "No more guesses, maybe next time?"
    noMoreGuesses.value = 1;
  }

  guess.value = '';
}

</script>

<template>
<h1>JUST HANG</h1>
<p>It's hangman without the man (because what kind of twisted children's game involves hanging a man if you don't guess the word?)</p>
<p>In this game, you will uncover the secret word in 12 rounds or fewer.</p>
<br>
<p>{{message}}</p>
<p>The secret word is <span class="secretWord">{{wordGuess}}</span></p>
<br>
<input type="text" v-model="guess" @keyup.enter="makeGuess(guess)" :disabled="noMoreGuesses">
<button @click.prevent="makeGuess(guess)" :disabled="noMoreGuesses">Guess</button>
<br>
<p>Guesses left: <span>{{guessCount}}</span></p>
</template>

<style scoped>

.secretWord {
  text-transform: uppercase;
  font-weight: bold;
}
</style>

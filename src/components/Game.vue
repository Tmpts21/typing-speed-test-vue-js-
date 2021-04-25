<template>

  <div>      
    <br> 
    <h2 class='text-center'>{{ msg }}</h2>              
    <div class="card border-dark mb-3 mx-auto mt-4" style="max-width: 18rem;">   
    <div class="card-header font-weight-bold">Typing stats    
    <button v-if ="tries.length != 0 " type="button" class="float-right btn-sm btn-primary" data-toggle="modal" data-target="#exampleModal"> 
      show all wpm   
    </button>
        </div>  
        <div class="card-body text-dark">  
          <p class="font-weight-bold card-text">avg wpm : {{Math.round(( avgWpm+ Number.EPSILON) * 100) / 100}}  wpm</p>       
          <p class="font-weight-bold card-text">accuracy :{{Math.round(( accuracy + Number.EPSILON) * 100 ) /100 }}% </p>         
          <p class="font-weight-bold card-text">words typed : {{wordsTyped}}</p>
          <p class="font-weight-bold card-text">tries :{{tries.length}} </p> 
        </div>
    </div>

    <h5 v-if="wpm != 0  && wpm >= 60 && wpm < 70 " class='text-center'>  
     <small class='text-center'> " This is the speed required for most high-end typing jobs " </small> 
     </h5>                  
    <h5 v-if="wpm != 0  && wpm >= 70 && wpm < 80  " class='text-center'>
     <small class='text-center'>" You are way above average! " </small> 
     </h5>                 
    <h5 v-if="wpm != 0  && wpm >= 80  " class='text-center'>  
     <small class='text-center'>" Your a catch  " </small>
     </h5>                  

    <h5 v-if="wpm != 0 " class='text-center'>Congratulations you have  {{ wpm }} words per minute </h5>              
    <div class='text-center'>   
    <a  v-if="wpm != 0 " class='text-center' href="https://www.wikihow.com/Calculate-Words-Per-Minute" style="border-radius : 80%">   what is wpm ?  </a> 
    </div>
    <h4 v-if ="game" class='float-right mr-5' style="padding-right :92px">{{ timer }}s</h4>              
    <h4 v-if ="game" class='float-left  ml-5' style="padding-left :92px">difficulty : {{ difficulty }}</h4>                
    <div v-if ="game"  class='w-75 mx-auto p-3 rounded mt-5 ' style="background-color : rgb(235, 232, 236)  ;">     
        <span v-for="(word , key) in words " :key="key"  >  
            <span v-if="key == wordCounter" 
                :style="{  
                  'color' : charBg ,  
                  'text-decoration' : 'underline', 
                }"
            >  
                {{words[wordCounter]}}  
            </span>   
            <span v-else>
              {{word.trim()}}
            </span>  
        </span>  
    </div>  

    <div class = 'text-center'>      
        <button class='btn btn-primary  mr-1 '  v-if="!game"  v-on:click="startGame()">start typing</button>        
        <select v-if="!game" v-model="difficulty" class="custom-select" style="width : 16% ">    
          <option selected>Select difficulty</option>
          <option value="easy">easy (40 words) </option>
          <option value="medium">medium (50 words) </option> 
          <option value="hard">hard (65 words)</option> </select>

        <input v-if="game" class='form-control w-75 mx-auto' v-model='typed' type="text" v-on:keydown="onPress" >     <br>  <br>  
    </div>    

    <!-- Modal -->
<div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">My words typing stats</h5> 
        <button type="button" class="close" data-dismiss="modal" aria-label="Close"> 
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body"> 
        <div v-for="(item , key ) in tries " :key="key"> 
          <div>
           try : 
           <span class = 'font-weight-bold'>
           {{key+ 1}} 
           </span>
          </div> 
          <div class= 'ml-5' > wpm :  {{Math.round(item.wpm * 100 ) / 100}} wpm</div>  
          <div class= 'ml-5' > accuracy :  {{item.accuracy}} % </div>    
          <div class= 'ml-5' > words typed : {{item.wordCount}}</div>     
          <div class= 'ml-5' > difficulty : {{item.difficulty}}</div>    
          </div> 
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
      </div>
    </div>
  </div>
</div>


  </div>   

</template> 

<script> 
import randomWords from 'random-words' 

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  }, 
  data () { 
    return {   
      game : false ,  
      words : [],   
      arrWordsCount: [] , 
      tries : [] , 
      wordCounter : 0 , 
      charCounter : 0 ,   
      typed : '' , 
      charBg: '' ,  
      wrongCharsPress : 0 , 
      charPress : 0 , 
      timer : 60 ,    
      difficulty : 'easy' , 
      //card stats  
      wpm : 0 ,  
      wordsTyped : 0  ,  
      avgWpm : 0 , 
      accuracy : 0, 
      timeOut : null 

    } 
  }, 
  mounted() {   
  } ,
  methods :  {    
    onPress(e) {                    
      if (e.key == "Backspace") {  
        return 
      }
      // if the user press space 
      if (e.key === " ") {     
        if (this.typed.trim() == this.words[this.wordCounter]) {  // check if the input is correct    
            this.typed = ''
            this.charCounter = 0 
            this.wordCounter += 1       
            this.wordsTyped += 1      
            this.charBg = 'green'   
            // check if the user finished typing  
            if (this.wordCounter  == this.words.length) {         
              // call end game method 
              return this.endGame();  
            }   
          return 
        }     
      }
      // add styling to words 
    this.charBg = this.words[this.wordCounter][this.charCounter] == e.key  ? 'green' : 'red'     
    if (this.charBg == 'green') {
        this.charCounter += 1 
        this.charPress +=1  // increment chars press 
        }    
    else { this.wrongCharsPress += 1 ;  } // increment wrong chars typed 
    },  

    startGame() {   
      this.game = true       
      this.wpm = 0     
      this.timer = 60 
      this.getRandomWords() // get random words 
      this.countDownTimer() // start timer 
    }, 
    
    getRandomWords() {    
      const difficulty = (          
        this.difficulty == 'easy' ? 40  :    
        this.difficulty == 'medium' ? 50 :  
        this.difficulty == 'hard' ? 65  :  
        40 // else 
      )
      this.words = randomWords(difficulty)    
    }, 

    calcWpm() {     
      this.wpm = ( this.wordCounter / (Math.abs(this.timer - 60 ) / 60   ) )  
    } ,    
 
    countDownTimer() {     
    if(this.timer > 0 ) {    
      this.timeOut = setTimeout(()=>{
         this.timer -= 1
         this.countDownTimer()}  
         ,1000);
    }     
    else { this.endGame() }    

  },   

  getAvgWpm() {          
    var wpmSum = 0    
    this.tries.forEach(element => {  
      wpmSum+= element.wpm 
    }); 
    this.avgWpm = wpmSum  / this.tries.length  
  } , 

  endGame() {   
      //calc wpm    
      this.calcWpm()   
      //push the new wpm
      this.tries.push({
        'wpm' : this.wpm ,  
        'wordCount' : this.getAllcharsCount()[1] ,
        'accuracy' : this.getAccuracy() , 
        'difficulty' : this.difficulty  
        })   
      //get avg wpm 
      this.getAvgWpm()    
      //reset the game    
      this.game = false     
      this.gameEnded = true      
      this.charCounter = 0   
      this.wordCounter = 0 
      this.typed = ''  
      this.wrongCharsPress = 0  
      this.charPress = 0  
      this.allCharsCount = 0       
      clearTimeout(this.timeOut)
  },  

  getAccuracy() {       
    const allCharsCount = this.getAllcharsCount()  
    this.accuracy = ( (this.charPress - this.wrongCharsPress) / allCharsCount[0] ) * 100      
    return Math.round((this.accuracy + Number.EPSILON)  * 100) / 100        
  }, 

  getAllcharsCount() {  
    var allCharsCount = 0  
    this.words.forEach(element => { 
      allCharsCount += element.length  
    });     
    return [allCharsCount  , this.words.length ] 
  } 

}  
}

</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>   
 
</style>
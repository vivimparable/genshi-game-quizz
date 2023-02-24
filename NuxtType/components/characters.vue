<template>
    <div class="container">
      <hr>
<!--Scoreand Rounds Data-->
    <div class="row">

        <div class="col-2">
            <h5>Score:</h5> <p>{{this.GlobalScore}}</p> 
            <h5>Round:</h5> <p>{{this.rounds}}</p> 
        </div>
         
<!--Message of error in case miss character-->
         <div v-if="this.message" class="col-10 alert alert-danger d-flex justify-content-center  align-items-center text-center" role="alert">
                <div>
                        <h3>{{message}}</h3>
                        <hr>
                        <p>-3 points OMG. ¡Try again!</p>
                </div>
        </div>

    </div>

        <div v-if="this.GlobalScore>=1" class="row text-center">

        
 <!--Buttons to chose a character-->           
            <div class="col-12 col-md-4 ">
                <button @click="changeIt(1)" class="btn-success btn buuttons-info">
                    Clue-1 (-0 points)
                </button>

                    <p class="buuttons-info" v-if="this.clues1">{{RightCharacter.nation}}</p> 

                <button v-if="this.clues1" @click="changeIt(2)" class="btn-warning btn buuttons-info">
                    Clue-2 (-1 Ponints)
                </button>

                    <p class="buuttons-info" v-if="this.clues2">{{RightCharacter.weapon}}</p> 

                <button v-if="this.clues2" @click="changeIt(3)" class="btn-danger btn buuttons-info">
                    Clue-3 (- 3 Points)
                </button>
                    <div v-if="this.clues3">
                        <p class="buuttons-info">{{RightCharacter.vision}}</p> 
                        <hr>
                        <p class="buuttons-info">¡There's no more clues!</p>
                        <img class="img-fluid" src="@/assets/X_X.jpg" alt="">
                    </div>
                    
            </div>
<!--characters to pick--> 
                   
            <div class="col-12 col-md-8 mt-md-0 mt-5">
                <ul v-for="a of this.fourCharacters" :key="a">
                    <li  @click="GetChampion(a.id)">{{a.name}}</li>
                </ul>
            </div>

        </div>
<!--In case the game is over-->

        <div class="buuttons-info p-2 text-center border border-info rounded" v-else>
            <h2 class="text-danger">Game Over</h2>
            
            <p>You passed {{this.rounds}} rounds on this game.</p>
            
            <button @click="tryAgain" class="btn btn-outline-info">Try again</button>
        </div>

        <div>
<!--Data to save the games 
        <p>Rounds:</p>
            <div v-for="i of roundsView" :key="i">
               <p>-Number of rounds passed: {{i}}</p> 
            </div>-->
        </div>

        

    </div>
</template>

<script lang="ts">
import {reactive,toRefs} from 'vue';
import axios from 'axios';
import Vue from 'vue';
import Swal from 'sweetalert2';
export default Vue.extend( {
    
    name:'characters',

    setup() {
        const state= reactive({

        })
        return{...toRefs(state)}
    },
    data(){
        return{
            characters:[] as {id:number, name:string} [] ,
            fourCharacters:[] as {id:number, name:string} [] ,
            fourNumbers:[]  as number[],
            mainNumber:0 as number,
            RightCharacter:[] as any,
            message:'' as string ,
            clues1:false as boolean,
            clues2:false as boolean,
            clues3:false as boolean,
            GlobalScore:20 as number,
            rounds:1 as number,
            roundsView:[] as number[]
        }
    }, async created(){

        await this.getapi();
        this.getRandomCharacter() 
        

    },methods:{
        async tryAgain(){
                this.message='';
                this.GlobalScore=20;
                this.fourCharacters =[];
                this.fourNumbers=[] ;
                this.RightCharacter=[];
                this.mainNumber=0;
                this.clues1=false;
                this.clues2=false;
                this.clues3=false;
                this.rounds=1;
                this.characters=[];

            await this.getapi();
        this.getRandomCharacter() 
        },
        changeIt(numero:number){

          switch (numero) {
            case 1:
                this.clues1 = true;
                break;
            case 2:
                
                if(this.clues2==false){
                    this.GlobalScore -=1
                    this.clues2 = true;
                }
                 
                break;
            
            case 3:
                if(this.clues3==false){
                    this.GlobalScore -=2
                     this.clues3 = true;
                }    
            break;
            default:
                break;
          }
        },
        
        GetChampion(id:number){    

            
            if(id==this.mainNumber){

                this.rounds++; 

                Swal.fire({
                position: 'center',
                icon: 'success',
                title: `¡Well done, you are on the ${this.rounds}th round!`,
                showConfirmButton: false,
                timer: 2500
                })
                
                
                this.message='';
                this.fourCharacters =[];
                this.fourNumbers=[] ;
                this.RightCharacter=[];
                this.mainNumber=0;
                this.clues1=false;
                this.clues2=false;
                this.clues3=false;

                this.getRandomCharacter();
                
                
               
            }else{

                this.message='You are wrong :C'  
                this.GlobalScore-=3; 
                setTimeout(() => {
                this.message=''
                }, 3000);
                
            }
        },
        
         getRandomCharacter(){
        //Four random numbers
        for(let i:number =1; i<=8; i++){

            this.fourNumbers.push(
                Math.floor(Math.random()* this.characters.length)
            )   
        }

        //Creating Array with the four random numbers
        let numero: number;
        for(numero of this.fourNumbers){
            this.fourCharacters.push(
                this.characters[numero]
            )    
        }
        this.getNumber();
        },
        

        async getNumber(){
            let numbers:any = Math.floor(Math.random() * 8);
           
            this.mainNumber =  this.fourNumbers[numbers];

            try {
 
                let dataOne = await axios.get('https://api.genshin.dev/characters/'+this.characters[this.mainNumber].name)
                let {data} = dataOne;
                this.RightCharacter=data;

            } catch (error) {
                
            }
        }, 

        async getapi(){
            try{
                let dataOne = await axios.get('https://api.genshin.dev/characters')
                let {data} = dataOne;
                let id = 0;
        //Characters
            for(let name of data){
                this.characters.push(
                {id:id++, name})
            }
            }catch (error) {
                this.$router.push({path:'/error'}) 
            }
        }
    
    },watch:{
        GlobalScore(value){
            if(value<=0){
                this.roundsView.push(
                    this.rounds
                )
            }
        }
    }


})
</script>

<style  scoped>
.buuttons-info{
    animation: shows 1s;
}

ul{
    padding: 0.1rem;
}

li{
    cursor: pointer;
    list-style: none;
    padding: 0.5rem;
    border-bottom: solid 0.1rem rgb(157 157 157); 
    transition: 0.1s;
}

li:hover{
        transform: scale(1.1,1.1);
        background-color: rgb(225 225 225);
    }

    img{
        width: 40%;
        animation: shows 1s 
    }

    .alert{
        height: 8rem;
    }

    @keyframes  shows{
    from {filter: opacity(0);}
    to {filter: opacity(default);} 
    }

   

</style>
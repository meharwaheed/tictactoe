<template>
  <q-page class="flex flex-center">

    <div v-if="game_ended">
      <q-btn color="orange-9" @click="restartGame" label="Start Game"/>
    </div>
    <div class="row  bg-orange-9 bordered" style="width: 450px;" v-else>
       <div class="col-4" v-for="(item, index) in 9" :key="item">
         <div  style="height: 100px; width:150px;" class="">
           <q-btn style="height: 100%;" class="full-width" @click="CalculateResult(item, index)">
              <q-icon size="xl" class="text-white" :name="fillIndex(item, index)"/>
           </q-btn>
         </div>
       </div>
    </div>

<!--    Game End Dialog Box-->
    <q-dialog v-model="game_ended_modal" persistent transition-show="scale" transition-hide="scale">
      <q-card class="bg-teal text-white" style="width: 300px">
        <q-card-section>
          <div class="text-h6">Cool! Game Ended</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          {{winner}}
        </q-card-section>

        <q-card-actions align="right" class="bg-white text-teal">
          <q-btn flat label="Close Game" v-close-popup />
          <q-btn flat label="Restart Game" @click="restartGame" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>

<!--    Player Names Dialog Box-->
    <q-dialog v-model="player_names_modal" persistent transition-show="scale" transition-hide="scale">
      <q-card style="width: 300px">
        <q-card-section>
          <div class="text-h6">Enter Player Names</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input v-model="first_player" label="Enter 1st Player Name"/>
        </q-card-section>
        <q-card-section class="q-pt-none">
          <q-input v-model="second_player" label="Enter 2nd Player Name"/>
        </q-card-section>

        <q-card-actions align="right" class="bg-white text-teal">
          <q-btn flat label="Start The Game" @click="startGame"/>
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'
import {Notify} from 'quasar'
export default defineComponent({
  name: 'IndexPage',
  data() {
    return {
        // boxes: Array(3).fill(Array(3).fill(0))
      boxes: [
        [null,null,null],
        [null,null,null],
        [null,null,null]
      ],
      is_first_player: 1,
      box_occupied: [],
      game_ended: false,
      game_ended_modal: false,
      winner: '',
      first_player: null,
      second_player: null,
      player_names_modal: false

    }
  },
  methods:
  {
    closeGame() {
      this.boxes = [
        [null,null,null],
        [null,null,null],
        [null,null,null]
      ]
      this.winner= ''
      this.box_occupied = []
      this.is_first_player = 1
    },
    restartGame(){
      this.boxes = [
        [null,null,null],
        [null,null,null],
        [null,null,null]
      ]
      this.game_ended = false
      this.winner= ''
      this.box_occupied = []
      this.is_first_player = 1
      this.player_names_modal = true
    },
    CalculateResult(item, index) {
       if(this.game_ended) {
         console.log('Game Eneded')
         return
       }
      if(!this.box_occupied.includes(item))
      {
        let detect = (item > 3 && item < 7) ? 3 : (item > 6) ? 6 : 0  // find column by clicking box
        let second_index = index - detect
        let player_icon = this.is_first_player ? 'close' : 'radio_button_unchecked'
        this.boxes[(Math.floor(index/3))][second_index]= player_icon
        this.is_first_player = 1- this.is_first_player
        this.box_occupied.push(item)
        this.findWinner()
      }

    },
    fillIndex(item, index) {
      let detect = (item > 3 && item < 7) ? 3 : (item > 6) ? 6 : 0
      return this.boxes[Math.floor(index/3)][index - detect]
    },
    findWinner(){
      let arr = this.boxes
      if(this.allSame(this.boxes[0])){
        this.game_ended_modal = true
      }
      else if (this.allSame(this.boxes[1])) {
        this.game_ended_modal = true
      }
      else if (this.allSame(this.boxes[2])) {
        this.game_ended_modal = true
      }
      else if (this.columnCheck(this.boxes, 1)) {
        this.game_ended_modal = true
      }
      else if (this.columnCheck(this.boxes, 2)) {
        this.game_ended_modal = true
      }
      else if (this.columnCheck(this.boxes, 3)) {
        this.game_ended_modal = true
      }
      else if (this.allSame([this.boxes[0][0], this.boxes[1][1], this.boxes[2][2]])) {
        this.game_ended_modal = true
      }
      else if (this.allSame([this.boxes[0][2], this.boxes[1][1], this.boxes[2][0]])) {
        this.game_ended_modal = true
      } else if(this.box_occupied.length == 9) {
        this.game_ended_modal = true
        this.winner = "Game Draw. No one has Won the Game"
      }
    },
    allSame(arr) {
       let is_all_same = arr.every(v => v && v === arr[0]);
       if(is_all_same) {
         this.game_ended = true
         if(arr[0] == 'close') {
           this.winner =  this.first_player + "  has Won the Game"
         } else {
           this.winner = this.second_player + " has Won the Game"
         }
       }
       return is_all_same
    },
    columnCheck(arr, columns_number) {
      const temp_arr = []
      for(let i=0; i< arr.length; i++) {
         temp_arr.push(arr[i][columns_number-1])
      }
      return this.allSame(temp_arr)
    },
    startGame() {
      if(this.first_player && this.second_player) {
        this.player_names_modal = false
      } else {
        Notify.create({
          color: 'negative',
          position: 'top',
          message: "Please enter the name of Both the Players",
          html:true,
          icon: 'report_problem'
        })
      }
    }
  },
  created() {
    this.player_names_modal = true
  }
})
</script>

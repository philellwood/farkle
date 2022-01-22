<template>
  <h2 v-if="farkled">Farkled!</h2>
  <div class="rolls">
    <h2>To Roll</h2>
    <die v-for="{value, index} of dice" :key="index" :value="value" @click="score(index, value)" />
  </div>
  <div class="bank">
    <h2>Bank</h2>
    <die v-for="{value, index} of bank" :key="index" :value="value" />
  </div>
  <div class="score">
    Total Score: {{totalScore}}
    Round Score: {{roundScore}}
  </div>
  <button @click="rollAll">Roll!</button>
  <button v-if="!firstRoll && !farkled" @click="takeScore">Take Score</button>
</template>
<script>
import Die from './Die.vue'

export default {
  components:{
    die: Die
  },
  data(){
    return {
      dice: [],
      bank: [],
      totalScore: 0,
      roundScore: 0,
      firstRoll: true,
      farkled: false
    }
  },
  mounted: function(){
    this.resetDice()
  },
  methods: {
    roll(){
      return Math.ceil(Math.random() * 6)
    }, 
    rollAll(){
      if (this.farkled)
        return this.resetDice()
      this.dice.forEach(die => die.value = this.roll())
      if (!this.firstRoll){
        this.farkled = false
        const isFarkle = this.checkFarkle()
        if (isFarkle){
          this.roundScore = 0
          this.farkled = true
        }
      }
    },
    score(index, value){
      this.firstRoll = false
      const multiple = this.checkMultiples(value)
      if (multiple >= 3){
        const multiplier = multiple - 2
        this.roundScore += (value == 1 ? 1000 : value * 100) * multiplier
      } else {
        switch (value) {
          case 5:
            this.roundScore += 50
            break;
          case 1:
            this.roundScore += 100
            break;
          default:
            break;
        }
      }
      if (multiple < Math.max(this.dice.length, 3)){
        const offset = this.bank.length
        const toBank = Array.from({length: multiple > 2 ? multiple : 1}, (v,i) => ({index: offset+i, value: value}))
        this.bank = [...this.bank, ...toBank]
        
        if (multiple >= 3){
          this.dice = this.dice.filter(d => d.value != value)
        } else {
          this.removeDie(index)
        }

        if (this.bank.length == 6){
          this.resetDice()
        }
      }
    },
    takeScore(){
      this.totalScore = this.roundScore
      this.roundScore = 0
      this.resetDice()
    },
    resetDice(){
      this.firstRoll = true
      this.farkled = false
      this.dice = Array.from({length:6}, (v,i) => ({index: i, value: i}))
      this.bank = []
      this.rollAll()
    },
    removeDie(index){
      this.dice.splice(this.dice.findIndex(d => d.index == index), 1)
    },
    checkMultiples(value){
      return this.dice.reduce((a, die) => value == die.value ? a + 1 : a, 0)
    },
    checkFarkle(){
      const values = new Set(this.dice.map(d => d.value))
      if (values.has(1) || values.has(5))
        return false
      
      const anyMultiples = [2,3,4,6].reduce((a, v) => a = a || this.checkMultiples(v) >= 3 , false)
      return !anyMultiples
    }
  }
}
</script>

<style scoped>
  .bank, .rolls{
    display: flex;
    justify-content: center;
    align-items: center;
    column-gap: 1rem;
  }
</style>



<template>
<v-layout
  column
  justify-center
  align-center>
  <div class="main">
    <h1 class="main-title">Calculator</h1>
    <div class="calculator">
      <table class="calculator-table" cellspacing="5">
        <tr>
          <td colspan="4">
            <!-- inputはタイピングによるユーザーの入力を受け付けない -->
            <input type="text" v-model="output" class="calculation-place" disabled>
          </td>
        </tr>
        <tr v-for="(row, index) in items" v-bind:key="index">
          <td v-for="(item, index) in row" v-bind:key="index" class="calculator-td">
            <v-btn v-on:click="calc(item)" x-large class="calculator-btn">{{ item }}</v-btn>
          </td>
        </tr>
      </table>
    </div>
  </div>
</v-layout>
</template>

<script>
import Logo from '~/components/Logo.vue'
import VuetifyLogo from '~/components/VuetifyLogo.vue'

export default {
  components: {
    Logo,
    VuetifyLogo
  },

  data (){
    return{
      output: '0',
      items: [
        ['', '√', 'C', '÷'],
        ['7', '8', '9', '×'],
        ['4', '5', '6', '-'],
        ['1', '2', '3', '+'],
        ['+/-', '0', '.', '=']
      ],
      isResult: false
    }
  },

  methods: {
    calc: function(sign){
      if(this.isResult){
        // =演算子を押した後かどうかの判断して何か入力した時に0に戻す
        this.output = '0'
        this.isResult = false
      }
      if(sign == ''){
        // 空白ボタン処理
        return
      }
      else if(sign == '+/-'){
        // ±変換処理
        if((this.output.slice(-1) == '÷') || (this.output.slice(-1) == '×') || (this.output.slice(-1) == '-') || (this.output.slice(-1) == '+')){
          var plusminus1 = -1 * eval(this.output.slice(0, -1))
          this.output += plusminus1
        }
        else{
          var plusminus2 = -1 * this.output.slice(-1)
          this.output = this.output.slice(0, -1) + plusminus2
        }
      }
      else if(sign === '='){
        try{
          // =を押したときの正常処理
          // ÷と×は正規表現ではないので変換
          this.output = this.output.replace('÷', '/')
          this.output = this.output.replace('×', '*')

          if((this.output.slice(-1) == '/') || (this.output.slice(-1) == '*') || (this.output.slice(-1) == '-') || (this.output.slice(-1) == '+')){
            // 計算式の最後が四則演算記号で終わる場合、その直前の式を計算してそれを後において計算する
            var end = eval(this.output.slice(0, -1))
            this.output = eval(this.output + end)
          }
          else{
            this.output = eval(this.output)
          }

          this.isResult = true
        }
        catch{
          // =を押したときのエラー処理
          this.output = "error"
        }
      }
      else if(sign === 'C'){
        // Cを押したときの処理
        this.output = '0'
      }
      else if(this.output === '0'){
        if(!isNaN(sign)){
          // 0だった場合に数字を打ち込んでも0何とはならずに数字が入力される処理
          this.output = sign
        }
        else{
          this.output += sign
        }
      }
      else{
        if((/\W+$/.test(this.output)) && (/\W+$/.test(sign))) {
          // 連続した記号は後に選択した記号にする処理
          // 小数点もこれで処理
          this.output = this.output.slice(0, -1)
          this.output += sign
        }
        else if(this.output == 'error'){
          // エラーが出た後の処理
          if(/\W+$/.test(sign)){
            // 記号ならば0と記号を組み合わせて表示
            this.output = '0'　+ sign
          }
          else{
            this.output = sign
          }
        }
        else{
          // 数字はそのまま入力
          this.output += sign
        }
      }
    }
  }
}
</script>

<style>
.main{
  width: 100%;
}

.main-title{
  font-size: 3em;
}

.calculation-place{
  text-align: right;
  background-color: rgb(88, 88, 88);
  color: #fff;
  border: 2px solid rgb(61, 61, 61);
  letter-spacing: 4px;
  width: 380px;
  height: 50px;
  font-size: 30px;
  padding: 5px;
}
</style>

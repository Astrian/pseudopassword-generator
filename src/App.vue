<template>
  <div id="app">
    <b-container>
      <b-row align-h="center">
        <b-col cols="6">
          <h1>虚词密码生成器</h1>
          <hr>
          <div class="result">
            {{generatedpassword}}
          </div>
          <div class="result-addon">
            <span>点击密码以复制</span>
            <button @click="generate">重新生成</button>
          </div>
          <hr>
          <h2>生成设置</h2>
          <form @submit.prevent="generate">
            <div class="field field-checkbox">
              <input type="checkbox" id="randomwordlength" :checked="true" @change="change" :true-value="true" :false-value="false">
              <label for="randomwordlength" class="checkboxlabel">密码中的虚词长度不定</label>
            </div>
            <div class="field" v-if="!randomwordlength">
              <label for="wordlength">每个虚词有多长？</label>
              <input id="wordlength" type="number" min="5" max="10" :value="6" @change="change">
            </div>
            <div class="field">
              <label for="wordcount">由多少个虚词组成？</label>
              <input id="wordcount" type="number" min="3" max="12" :value="5" @change="change">
            </div>
            <div class="field">
              <label for="worddivider">虚词之间用什么符号分割？</label>
              <select id="worddivider" :value="worddivider" @change="change">
                <option :value="1">横线（-）</option>
                <option :value="2">美元符号（$）</option>
                <option :value="3">数字 0</option>
                <option :value="4">下划线（_）</option>
                <option :value="0">自定义</option>
              </select>
            </div>
            <div class="field" v-if="worddivider === 0">
              <label for="customdivider">自定义虚词间分隔符</label>
              <input id="customdivider" maxlength="1" placeholder="在此输入一个字符，留空代表不分割" @change="change">
            </div>
          </form>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import pseudoword from 'pseudoword'

export default {
  name: 'App',
  data() {
    return {
      wordlength: 6,
      randomwordlength: true,
      wordcount: 5,
      worddivider: 1,
      customdivider: "",
      generatedpassword: ""
    }
  },
  methods: {
    generate() {
      let result = ""
      let divider = ["-", "$", "0", "_"]
      for (let i = 0; i < this.wordcount; i++) {
        if (i!== 0 ) {
          if (this.worddivider === 0) {
            result += this.customdivider
          } else {
            result += divider[this.worddivider - 1]
          }
        }
        if (this.randomwordlength) {
          result += pseudoword(Math.floor(Math.random() * 10) + 5)
        } else {
          result += pseudoword(this.wordlength)
        }
      }
      this.generatedpassword = result
    },
    change(sth) {
      let id = sth.target.id
      if (id === "wordlength") {
        let value = parseInt(sth.target.value)
        if (isNaN(value) || value > 10 || value < 5) {
          return
        }
        this.wordlength = value
      } else if (id === "wordcount") {
        let value = parseInt(sth.target.value)
        if (isNaN(value) || value > 12 || value < 3) {
          return
        }
        this.wordcount = value
      } else if (id === "worddivider") {
        let value = parseInt(sth.target.value)
        this.worddivider = value
      } else if (id === "customdivider") {
        let value = sth.target.value
        console.log(value)
        this.customdivider = value
      } else if (id === "randomwordlength") {
        this.randomwordlength = sth.target.checked
      }
      this.generate()
    }
  },
  mounted() {
    console.log("mounted")
    this.generate()
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
.result {
  font-family: Menlo, Monaco, 'Courier New', monospace;
  text-align: center;
  background: rgb(235, 235, 208);
  padding: 30px;
  cursor: pointer;
  border-radius: 4px;
}
.result-addon {
  display: flex;
  margin-top: 10px;
  justify-content: space-between;
  align-items: center;
}
.result-addon span {
  color: gray;
}
.field {
  display: flex;
  flex-direction: column;
  margin: 20px 0;
}
.field-checkbox {
  flex-direction: row;
}
.field-checkbox input {
  margin-right: 10px;
}
.field label {
  font-weight: bold;
  padding-top: 9px;
}
.field label.checkboxlabel {
  font-weight: normal;
}
.field input {
  border: none;
  padding: 8px;
  background: rgba(0, 0, 0, 0.048);
  border-radius: 4px;
  outline: none;
}
.field select {
  border: none;
  padding: 8px;
  background: rgba(0, 0, 0, 0.048);
  border-radius: 4px;
  outline: none;
}
button {
  border: none;
  background-color: rgb(78, 160, 214);
  color: white;
  padding: 5px 20px;
  border-radius: 4px;
  font-weight: bold;
}
button:active {
  box-shadow: inset 0px 4px 12px -4px rgba(0, 0, 0, 0.219);
  background-color: rgb(46, 134, 192);
}
</style>

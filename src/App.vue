<template>
  <div id="app">
    <b-container>
      <b-row align-h="center">
        <b-col md="6">
          <h1>虚词密码生成器</h1>
          <hr>
          <div class="result" @click="copy">
            {{generatedpassword}}
          </div>
          <div class="result-addon">
            <span>点击密码以复制</span>
            <button @click="generate">重新生成</button>
          </div>
          <hr>
          <h2>生成设置</h2>
          <b-alert show variant="warning" v-if="generatedpassword.length > 63">
            <b class="warning-title">⚠️ 密码太长</b><br>
            <span class="warning-content">这个密码长度可能超过 WPA2 加密协议（或其他地方）的密码长度上限。</span>
          </b-alert>
          <form @submit.prevent="generate">
            <div class="field field-checkbox">
              <div class="checkbox-body">
                <input type="checkbox" id="randomwordlength" :checked="true" @change="change" :true-value="true" :false-value="false">
                <label for="randomwordlength" class="checkboxlabel">虚词长度不定</label>
              </div>
              <span class="helptext">选中后，虚词长度将会在 3 至 8 之间随机。</span>
            </div>
            <div class="field" v-if="!randomwordlength">
              <label for="wordlength">每个虚词有多长？</label>
              <input id="wordlength" type="number" :value="wordlength" @change="change">
              <span class="helptext">每个虚词至少要由 3 个字母组成。</span>
            </div>
            <div class="field">
              <label for="wordcount">由多少个虚词组成？</label>
              <input id="wordcount" type="number" :value="wordcount" @change="change">
              <span class="helptext">至少需要 3 个虚词组成密码。</span>
            </div>
            <div class="field">
              <label for="worddivider">虚词之间用什么符号分割？</label>
              <select id="worddivider" :value="worddivider" @change="change">
                <option :value="1">横线（-）</option>
                <option :value="2">美元符号（$）</option>
                <option :value="3">数字 0</option>
                <option :value="4">下划线（_）</option>
                <option :value="0">自定义（不推荐）</option>
              </select>
            </div>
            <div class="field" v-if="worddivider === 0">
              <label for="customdivider">自定义虚词间分隔符</label>
              <input id="customdivider" maxlength="1" placeholder="在此输入一个字符，留空代表不分割" @change="change">
            </div>
          </form>
          <hr>
          <h2>一些说明</h2>
          <p>这是一个用于随机生成由虚词组成的密码的工具。</p>
          <p>「虚词」（pesudoword）是指本身不是有意义英语单词、但拼写上符合发音规则的单词。这种单词拥有一定随机性，但更容易被人（短期地）记住。</p>
          <p>在家用 WLAN 热点部署过程中，如果使用 <a href="https://zh.wikipedia.org/wiki/WPA" target="_blank">WPA2-Personal（WPA2-PSK）</a>加密模式，其安全性与密码随机性相关。但过于随机的字符串难以手动输入，因此兼具随机性和「可念性」的虚词密码较为适合作为 Wi-Fi 密码使用。<a href="https://mp.weixin.qq.com/s/wPx-wELB6-ghzLq1S_2MdQ">详细…</a></p>
          <p>本工具使用包 <a href="https://www.npmjs.com/package/pseudoword" target="_blank">pseudoword</a> 来生成密码，全过程皆由您的设备完成。生成的密码不会发送至任何服务器，也不会被保存。您可以前往<a href="https://github.com/Astrian/pseudopassword-generator" target="_blank">本项目仓库</a>进行代码审计。</p>
          <hr>
          <p class="footer">Made by Astrian<br><a href="https://www.beian.miit.gov.cn/" target="_blank">粤 ICP 备 16028008 号 - 1</a></p>
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
      wordlength: 5,
      randomwordlength: true,
      wordcount: 4,
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
        if (i !== 0 ) {
          if (this.worddivider === 0) {
            result += this.customdivider
          } else {
            result += divider[this.worddivider - 1]
          }
        }
        if (this.randomwordlength) {
          result += pseudoword(Math.floor(Math.random() * 6) + 3)
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
        if (isNaN(value) || value < 3) {
          value = 3
        }
        this.wordlength = value
      } else if (id === "wordcount") {
        let value = parseInt(sth.target.value)
        if (isNaN(value) || value < 3) {
          value = 3
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
    },
    copy() {
      navigator.clipboard.writeText(this.generatedpassword)
      .then(() => {
        alert("密码已被复制。")
      }).catch(() => {
        alert("未能成功复制，请尝试手动选中复制。")
      })
    }
  },
  mounted() {
    console.log("mounted")
    this.generate()
  }
}
</script>

<style>
input[type=number] {
  -moz-appearance: textfield;
}
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
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
  line-break: anywhere;
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
.field .helptext {
  color: gray;
  font-size: 12px;
}
.field-checkbox .checkbox-body {
  flex-direction: row;
}
.field-checkbox .checkbox-body input {
  margin-right: 10px;
}
.field label {
  font-weight: bold;
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
  margin-bottom: 5px;
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
.footer {
  color: gray;
  font-size: 12px;
}
.footer a {
  color: gray;
}
.warning-title {
  font-size: 18px;
}
.warning-content {
  font-size: 14px;
}
@media (prefers-color-scheme: dark) {
  html, body, #app {
    background-color: #202020;
    color: rgb(226, 218, 218);
  }

  .result {
    color: black;
  }

  .result-addon span {
    color: rgba(226, 218, 218, 0.747);
  }

  a, a:hover {
    color: rgb(65, 181, 235);
  }

  .field .helptext {
    color: rgba(226, 218, 218, 0.747);
  }

  .field input {
    background: rgba(255, 255, 255, 0.288);
    color: rgb(226, 218, 218);
  }

  .field select {
    background: rgba(255, 255, 255, 0.288);
    color: rgb(226, 218, 218);
  }

  .footer, .footer a {
    color: rgba(226, 218, 218, 0.747);
  }

  hr {
    border-color: rgba(226, 218, 218, 0.212);
  }
}
</style>

<template>
  <Editor :language="language" :key="foo" v-model="input" theme="vs-dark"/>
  <!-- <textarea v-model="input" cols="30" rows="10"></textarea> -->
  
  <!-- <div>Response: {{ output }}</div> -->
  <select @change="selectLang" v-model="selectKey">
    <option v-for="lang in languageSelect" :key="languageSelect.indexOf(lang)" :value="languageSelect.indexOf(lang)">{{ lang.language }} - {{ lang.version }}</option>
  </select>
  <button @click="getResult">Run</button><br>
  <div v-html="htmlOutput"></div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import piston from 'piston-client'
import AnsiUp from 'ansi_up'
import Editor from '@/components/Editor.vue'


export default {
  name: 'App',
  components: {
    Editor
  },
  data() {
    return {
      ansi: undefined,
      output: {run: {}},
      input: '',
      foo: 0,
      language: '',
      languages: [
        {
          'language': 'bash',
          'demo': 'echo "Hello, World!"'
        },
        {
          'language': 'c++',
          'demo': '#include <iostream>\nusing namespace std;\nint main(){\n\tcout<<"Hello, World!";\n\treturn 0;\n}'
        },
        {
          'language': 'python',
          'demo': 'print("Hello, World!")'
        },
        
      ],
      selectKey: '',
      languageSelect: [],
    }
  },
  methods: {
    async getResult(){
      const client = piston({ server: "https://emkc.org" });
      this.output = await client.execute(this.language, this.input);
      
    },
    async getLanguages(){
      const client = piston({ server: "https://emkc.org" });
      const runtimes = await client.runtimes();

      for (const lang in runtimes) {
        if (Object.hasOwnProperty.call(runtimes, lang)) {
          const element = runtimes[lang];
          if(this.languages.find(el => {return el.language === element.language}) ){
            this.languageSelect.push(element)
          }
        }
      }
    },
    selectLang(){
      this.input = this.languages[this.selectKey].demo
      this.language = this.languages[this.selectKey].language
      this.foo++
    }
  },
  mounted(){
    this.getLanguages()
  },
  computed: {
    htmlOutput(){
      return this.ansi.ansi_to_html(this.output.run.output).replace(/\n/gm, '<br>')
    }
  },
  beforeMount () {
    this.ansi = new AnsiUp()
  },
}
</script>

<style>

</style>

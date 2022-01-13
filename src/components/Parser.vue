<template>
  <v-row class="pa-2">
    <v-col
        class="source pr-1"
        cols="6">
      <v-textarea
          outlined

          rows="15"

          v-model="inputString"

          label="JSON String"
          style="height: 100%"
          class=""></v-textarea>
    </v-col>
    <v-col
        class="result pl-1"
        cols="6">
      <v-card>
        <v-card-title class="pa-2" style="font-size: 16px; background: #cccccc" >
          Parsed Result

          <v-spacer></v-spacer>
          <v-menu>
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                  icon

                  v-bind="attrs"
                  v-on="on"
              >
                <v-icon>mdi-menu</v-icon>
              </v-btn>
            </template>

            <v-list>
              <v-list-item
                  v-for="(item, index) in resultTypesList"
                  :key="index"

                  @click.prevent="resultType = item.title"
              >
                <v-list-item-title>{{ item.title }}</v-list-item-title>
              </v-list-item>
            </v-list>

          </v-menu>
        </v-card-title>
        <template v-if="resultType === 'HTML'">
          <pre
              v-if="parsedResult"

              v-html="parsedResult"

              class="html"></pre>
        </template>
        <template v-else-if="resultType === 'String'">
          <div
              v-if="parsedResult"

              class="string pa-2">{{ parsedResult }}</div>
        </template>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: "Parser",
  data : ()=>({
    inputString: null,
    parsedResult: null,
    resultType: 'HTML',
    resultTypesList: [
      {
        title: 'HTML'
      },
      {
        title: 'String'
      }
    ]
  }),
  methods: {
     analyse(string) {
      let parsed = this.parseTheData(string);
      if(!parsed) {
        return string;
      }

      return this.recursiveParse(parsed);
    },
    recursiveParse(obj){
      for(let i in obj) {
        if(typeof obj[i] === "string" && this.parseTheData(obj[i])) {
          obj[i] = this.parseTheData(obj[i]);
          obj[i] = this.recursiveParse(obj[i]);
        }
      }

      return obj;
    },
    parseTheData(string) {
      try {
        return JSON.parse(string);
      } catch (error) {
        //console.log(error);
        return false
      }
    },
    showData(){
       switch (this.resultType){
         case 'HTML':
           this.parsedResult = this.inputString ? this.syntaxHighlight(JSON.stringify(this.analyse(this.inputString), null, 4)) : null
           break;
         case 'String':
           this.parsedResult = this.inputString ? JSON.stringify(this.analyse(this.inputString), null, 4) : null

           break;
       }
    },
    syntaxHighlight(json) {
      json = json.replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;');
      return json.replace(/("(\\u[a-zA-Z0-9]{4}|\\[^u]|[^\\"])*"(\s*:)?|\b(true|false|null)\b|-?\d+(?:\.\d*)?(?:[eE][+-]?\d+)?)/g, function (match) {
        var cls = 'number';
        if (/^"/.test(match)) {
          if (/:$/.test(match)) {
            cls = 'key';
          } else {
            cls = 'string';
          }
        } else if (/true|false/.test(match)) {
          cls = 'boolean';
        } else if (/null/.test(match)) {
          cls = 'null';
        }
        return '<span class="' + cls + '">' + match + '</span>';
      });
    }
  },
  watch: {
    inputString(){
      this.showData()
      //this.parsedResult = val ? this.syntaxHighlight(JSON.stringify(this.analyse(val), null, 4)) : null
    },
    resultType(){
      this.showData()
    }
  }
}
</script>

<style >
pre {padding: 5px; overflow-x: auto }
.string { color: green; }
.number { color: darkorange; }
.boolean { color: blue; }
.null { color: magenta; }
.key { color: #0e2b5a; }
</style>

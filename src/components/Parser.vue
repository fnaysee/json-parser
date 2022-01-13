<template>
  <div>
    <div class="head">
      Silence is golden!
    </div>
    <div class="source">
      <input type="text" v-model="inputString">
    </div>
    <div class="result">
      {{parsedResult}}
    </div>
  </div>
</template>

<script>
export default {
  name: "Parser",
  data : ()=>({
    inputString: null,
    parsedResult: null
  }),
  methods: {
     analyse(string) {
      let parsed = this.parseTheData(string);
      if(!parsed) {
        return string;
      }

      return this.loop(parsed);
    },
    loop(obj){
      for(let i in obj) {
        if(typeof obj[i] === "string" && this.parseTheData(obj[i])) {
          obj[i] = this.parseTheData(obj[i]);
          obj[i] = this.loop(obj[i]);
        }
      }

      return obj;
    },
    parseTheData(string) {
      try {
        return JSON.parse(string);
      } catch (error) {
        console.log(error);
        return false

      }
    }
  },
  watch: {
    inputString(val){
      this.parsedResult = this.analyse(val)
    }
  }
}
</script>

<style scoped>

</style>

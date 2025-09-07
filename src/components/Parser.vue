<template>
  <v-row class="pa-2 fill-height no-gutters">
    <!-- ستون ورودی -->
    <v-col
        class="source pr-1 d-flex flex-column"
        :cols="leftExpanded ? 10 : (rightExpanded ? 2 : 6)"
    >
      <div class="d-flex align-center mb-1">
        <span class="font-weight-bold mr-auto">JSON String</span>
        <v-btn small icon @click="toggleLeft">
          <v-icon>
            {{ leftExpanded ? 'mdi-arrow-collapse-horizontal' : 'mdi-arrow-expand-horizontal' }}
          </v-icon>
        </v-btn>
      </div>
      <v-textarea
          outlined
          rows="15"
          v-model="inputString"
          label="JSON String"
          class="flex-grow-1"
          style="min-height: 200px"
      />
    </v-col>

    <!-- ستون خروجی -->
    <v-col
        class="result pl-1 d-flex flex-column"
        :cols="rightExpanded ? 10 : (leftExpanded ? 2 : 6)"
    >
      <v-card class="d-flex flex-column fill-height">
        <v-card-title
            class="pa-2"
            style="font-size: 16px; background: #cccccc"
        >
          Parsed Result
          <v-spacer></v-spacer>

          <!-- دکمه اکسپند -->
          <v-btn small icon @click="toggleRight">
            <v-icon>
              {{ rightExpanded ? 'mdi-arrow-collapse-horizontal' : 'mdi-arrow-expand-horizontal' }}
            </v-icon>
          </v-btn>

          <!-- منو -->
          <v-menu>
            <template v-slot:activator="{ on, attrs }">
              <v-btn icon v-bind="attrs" v-on="on">
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

        <!-- محتوای خروجی -->
        <v-card-text class="parsed-wrapper flex-grow-1">
          <template v-if="resultType === 'HTML'">
            <div v-if="parsedResult" class="parsed-code-box">
              <json-viewer :data="parsedResult"  />
            </div>
          </template>

          <template v-else-if="resultType === 'String'">
            <div v-if="parsedResult" class="string pa-2">{{ parsedResult }}</div>
          </template>
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
import JsonViewer from "@/components/JsonViewer.vue";

export default {
  name: "Parser",
  components: { JsonViewer },
  data: () => ({
    inputString: null,
    parsedResult: null,
    resultType: "HTML",
    resultTypesList: [{ title: "HTML" }, { title: "String" }],
    leftExpanded: false,
    rightExpanded: false,
  }),
  methods: {
    toggleLeft() {
      this.leftExpanded = !this.leftExpanded;
      if (this.leftExpanded) this.rightExpanded = false;
    },
    toggleRight() {
      this.rightExpanded = !this.rightExpanded;
      if (this.rightExpanded) this.leftExpanded = false;
    },
    analyse(string) {
      let parsed = this.parseTheData(string);
      if (!parsed) return string;
      return this.recursiveParse(parsed);
    },
    recursiveParse(obj) {
      for (let i in obj) {
        if (typeof obj[i] === "string" && this.parseTheData(obj[i])) {
          obj[i] = this.parseTheData(obj[i]);
          obj[i] = this.recursiveParse(obj[i]);
        }
      }
      return obj;
    },
    parseTheData(string) {
      try {
        return JSON.parse(string);
      } catch {
        return false;
      }
    },
    showData() {
      const analysed = this.inputString ? this.analyse(this.inputString) : null;
      switch (this.resultType) {
        case "HTML":
          this.parsedResult = analysed;
          break;
        case "String":
          this.parsedResult = analysed
              ? JSON.stringify(analysed, null, 4)
              : null;
          break;
      }
    },
  },
  watch: {
    inputString() {
      this.showData();
    },
    resultType() {
      this.showData();
    },
  },
};
</script>

<style>
/* هر دو ستون فلکس و پرکننده ارتفاع */
.source,
.result {
  height: calc(100vh - 100px); /* بسته به هدر یا navbar تنظیم کن */
  display: flex;
  flex-direction: column;
}

/* کارت خروجی فلکس */
.result .v-card {
  flex: 1;
  display: flex;
  flex-direction: column;
}

/* محتوای داخلی کارت */
.parsed-wrapper {
  flex: 1;
  overflow: hidden;
  padding: 0 !important;
}

/* باکس JSON */
.parsed-code-box {
  height: 100%;
  overflow: auto;
  padding: 8px;
}
</style>

<template>
  <div class="json-viewer">
    <template v-if="isObject">
      <div>
        <span @click="toggle" class="toggle">
          {{ collapsed ? '+' : '-' }}
        </span>
        <span class="bracket">{</span>
        <span class="info-count"> {{ Object.keys(data).length }} keys</span>
        <template v-show="!collapsed" class="children" v-if="!collapsed">
          <div v-for="(value, key) in data" :key="key" class="child">
            <span class="key">"{{ key }}"</span><span>:</span>
            <json-viewer :data="value" class="value-inline"/>
          </div>
        </template>
        <span class="bracket">}</span>
      </div>
    </template>

    <template v-else-if="isArray">
      <div>
        <span @click="toggle" class="toggle">
          {{ collapsed ? '+' : '-' }}
        </span>
        <span class="bracket">[</span>
        <span class="info-count"> {{ data.length }} items</span>
        <div v-show="!collapsed" class="children" v-if="!collapsed">
          <div v-for="(value, key) in data" :key="key" class="child">
            <div v-for="(value, index) in data" :key="index" class="child">
              <json-viewer :data="value" class="value-inline"/>
            </div>
          </div>
        </div>
        <span class="bracket">]</span>
      </div>
    </template>

    <template v-else>
      <span :class="valueClass">{{ formatValue(data) }}</span>
    </template>
  </div>
</template>

<script>
export default {
  name: "JsonViewer",
  props: {
    data: {type: [Object, Array, String, Number, Boolean, null], required: true}
  },
  data: () => ({
    collapsed: true
  }),
  computed: {
    isObject() {
      return this.data && typeof this.data === "object" && !Array.isArray(this.data);
    },
    isArray() {
      return Array.isArray(this.data);
    },
    valueClass() {
      if (typeof this.data === "string") return "string";
      if (typeof this.data === "number") return "number";
      if (typeof this.data === "boolean") return "boolean";
      if (this.data === null) return "null";
      return "";
    }
  },
  methods: {
    toggle() {
      this.collapsed = !this.collapsed;
    },
    formatValue(value) {
      if (typeof value === "string") return `"${value}"`;
      if (value === null) return "null";
      return value;
    }
  }
};
</script>

<style scoped>
.json-viewer {
  font-family: monospace;
  font-size: 14px;
  line-height: 1.4;
}

.key {
  color: #0e2b5a;
  margin-right: 5px;
}

.string {
  color: green;
}

.number {
  color: darkorange;
}

.boolean {
  color: blue;
}

.null {
  color: magenta;
}

.bracket {
  font-weight: bold;
}

.info-count {
  font-size: 12px;
  color: #888;
  margin-left: 4px;
}

.children {
  padding-left: 10px; /* قبلاً 20px بود */
  border-left: 1px dotted #ccc; /* راهنما برای درک تو رفتگی */
  margin-left: 4px;
}

.toggle {
  cursor: pointer;
  color: #555;
  margin-right: 5px;
}

.child {
  margin: 2px 0;
  padding-left: 20px;
}
.child > .key {
  margin-right: 5px;
}
.inline {
  display: inline;
}

.value-inline {
  display: inline;
}
</style>

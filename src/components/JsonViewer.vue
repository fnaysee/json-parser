<template>
  <div class="json-viewer">
    <!-- Object -->
    <template v-if="isObject">
      <div>
        <span @click="toggle" class="toggle">
          {{ collapsed ? '+' : '-' }}
        </span>
        <span class="bracket">{</span>
        <span class="info-count" @click="toggle">{{ Object.keys(data).length }} keys</span>

        <!-- مهم: v-if برای lazy render -->
        <div v-if="!collapsed" class="children">
          <div v-for="(value, key) in data" :key="key" class="child">
            <span class="key">"{{ key }}"</span><span>:</span>
            <json-viewer
                :data="value"
                :default-collapsed="true"
                class="value-inline"
            />
          </div>
        </div>

        <span class="bracket">}</span>
      </div>
    </template>

    <!-- Array -->
    <template v-else-if="isArray">
      <div>
        <span @click="toggle" class="toggle">
          {{ collapsed ? '+' : '-' }}
        </span>
        <span class="bracket">[</span>
        <span @click="toggle" class="info-count">{{ data.length }} items</span>

        <!-- کنترل دکمه‌ها فقط وقتی بازه -->
        <span class="controls" v-if="!collapsed">
          <button @click="expandOneLevel">Expand 1 Level</button>
        </span>

        <!-- مهم: v-if برای destroy/restore بچه‌ها -->
        <div v-if="!collapsed" class="children">
          <div v-for="(value, index) in data" :key="index" class="child">
            <json-viewer
                ref="children"
                :data="value"
                :default-collapsed="true"
                class="value-inline"
            />
          </div>
        </div>

        <span class="bracket">]</span>
      </div>
    </template>

    <!-- Primitive -->
    <template v-else>
      <span :class="valueClass">{{ formatValue(data) }}</span>
    </template>
  </div>
</template>

<script>
export default {
  name: "JsonViewer",
  props: {
    data: {
      type: [Object, Array, String, Number, Boolean, null],
      required: true
    },
    defaultCollapsed: {
      type: Boolean,
      default: true
    },
    isRoot: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      collapsed: this.isRoot ? false : this.defaultCollapsed
    };
  },
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
    },
    expandOneLevel() {
      if (this.$refs.children) {
        this.$refs.children.forEach(child => {
          if (child && (child.isObject || child.isArray)) {
            child.collapsed = false;
          }
        });
      }
    },
    collapseAll() {
      if (this.$refs.children) {
        this.$refs.children.forEach(child => {
          if (child && (child.isObject || child.isArray)) {
            child.collapsed = true;
            if (typeof child.collapseAll === "function") {
              child.collapseAll();
            }
          }
        });
      }
      this.collapsed = true;
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

.key { color: #0e2b5a; margin-right: 5px; }
.string { color: green; }
.number { color: darkorange; }
.boolean { color: blue; }
.null { color: magenta; }

.bracket { font-weight: bold; }
.info-count { font-size: 12px; color: #888; margin-left: 4px; }

.children {
  padding-left: 10px;
  border-left: 1px dotted #ccc;
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
.child > .key { margin-right: 5px; }

.value-inline { display: inline; }

.controls { margin-left: 10px; }
.controls button {
  font-size: 11px;
  margin-left: 4px;
  padding: 0 4px;
  cursor: pointer;
}
</style>

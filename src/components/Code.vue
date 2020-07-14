<template>
  <div class="code-root">
    <div class="codes-box">
      <button class="run-btn" @click="runCode">运行</button>
      <codemirror
        id="codemirror-html"
        v-model="htmlCode"
        :options="cmOptions"
        @cursorActivity="runCode"
      />
      <codemirror
        id="codemirror-css"
        v-model="cssCode"
        :options="cmOptionsCss"
        @cursorActivity="runCode"
      />
      <codemirror
        id="codemirror-js"
        v-model="jsCode"
        :options="cmOptions"
        @cursorActivity="runCode"
      />
    </div>
    <div id="iframewrapper">
      <iframe id="iframeResult" frameborder="0"></iframe>
    </div>
  </div>
</template>
<script>
import dedent from "dedent";
import axios from "axios";
import { codemirror } from "vue-codemirror";
// base style
import "codemirror/lib/codemirror.css";
// theme css
import "codemirror/theme/base16-dark.css";
// language
import "codemirror/mode/vue/vue.js";
import "codemirror/mode/css/css.js"
// active-line.js
import "codemirror/addon/selection/active-line.js";
// styleSelectedText
import "codemirror/addon/selection/mark-selection.js";
import "codemirror/addon/search/searchcursor.js";
// highlightSelectionMatches
import "codemirror/addon/scroll/annotatescrollbar.js";
import "codemirror/addon/search/matchesonscrollbar.js";
import "codemirror/addon/search/searchcursor.js";
import "codemirror/addon/search/match-highlighter.js";
// keyMap
import "codemirror/mode/clike/clike.js";
import "codemirror/addon/edit/matchbrackets.js";
import "codemirror/addon/comment/comment.js";
import "codemirror/addon/dialog/dialog.js";
import "codemirror/addon/dialog/dialog.css";
import "codemirror/addon/search/searchcursor.js";
import "codemirror/addon/search/search.js";
import "codemirror/keymap/sublime.js";
// foldGutter
import "codemirror/addon/fold/foldgutter.css";
import "codemirror/addon/fold/brace-fold.js";
import "codemirror/addon/fold/comment-fold.js";
import "codemirror/addon/fold/foldcode.js";
import "codemirror/addon/fold/foldgutter.js";
import "codemirror/addon/fold/indent-fold.js";
import "codemirror/addon/fold/markdown-fold.js";
import "codemirror/addon/fold/xml-fold.js";

export default {
  name: "Code",
  props: {},
  components: {
    codemirror
  },
  data() {
    return {
      htmlCode: "",
      cssCode: "",
      jsCode: "",
      linkCode: "",
      cmOptions: {
        tabSize: 4,
        foldGutter: true,
        styleActiveLine: true,
        lineNumbers: true,
        line: true,
        mode: "text/html",
        theme: "base16-dark"
      },
      cmOptionsCss: {
        tabSize: 4,
        foldGutter: true,
        styleActiveLine: true,
        lineNumbers: true,
        line: true,
        mode: "text/css",
        theme: "base16-dark"
      }
    };
  },
  computed: {},
  watch: {},
  methods: {
    runCode() {
      var text = `<!DOCTYPE html><html lang="zh_CN"><head><meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0">`;
      // text += `<link rel="stylesheet" href="https://unpkg.com/element-ui/lib/theme-chalk/index.css">`;
      text += this.linkCode;
      text += `<style>${this.cssCode}</style>`;
      text += `</head>`;
      text += `<body>${this.htmlCode}</body>`;
      // text += `<script src="https://unpkg.com/vue/dist/vue.js">${"<\/script>"}<script src="https://unpkg.com/element-ui/lib/index.js">${"<\/script>"}`;
      // text += `<script>${this.jsCode}${"<\/script>"}`;
      text += this.jsCode;
      text += `</html>`;
      var ifr = document.createElement("iframe");
      ifr.setAttribute("frameborder", "0");
      ifr.setAttribute("id", "iframeResult");
      ifr.setAttribute("allowfullscreen", "true");
      ifr.style.overflow = "hidden";
      document.getElementById("iframewrapper").innerHTML = "";
      document.getElementById("iframewrapper").appendChild(ifr);

      var ifrw = ifr.contentWindow
        ? ifr.contentWindow
        : ifr.contentDocument.document
        ? ifr.contentDocument.document
        : ifr.contentDocument;
      ifrw.document.open();
      ifrw.document.write(text);
      ifrw.document.close();
    }
  },
  created() {},
  mounted() {
    axios.get("./data/button.html").then(res => {
      // 将body中的内容取出来
      let patternBody = /<body[^>]*>([\s\S]+?)<\/body>/i;
      let bodys = patternBody.exec(res.data);
      if (bodys) {
        this.htmlCode = bodys[1];
      }

      // 取出所有的script标签
      let patternCode = /<script[^>]*>((.|[\n\r])*)<\/script>/im;
      let codes = patternCode.exec(res.data);
      if (codes) {
        this.jsCode = codes[0];
      }

      // 取出自定义样式
      let patternStyle = /<style[^>]*>((.|[\n\r])*)<\/style>/im;
      let styles = patternStyle.exec(res.data);
      if (styles) {
        this.cssCode = styles[1];
      }

      // 取出组件库引入样式
      let patternLink = /<link[^>]*>((.|[\n\r])*)<\/link>/im;
      let links = patternLink.exec(res.data);
      if (links) {
        this.linkCode = links[0];
      }
    });
    // this.runCode();
  },
  beforeDestroy() {}
};
</script>
<style lang='scss' scoped>
.code-root {
  width: 100%;
  height: 100%;
  display: flex;
  .codes-box {
    position: relative;
    width: 50%;
    height: 100%;
    .run-btn {
      position: absolute;
      top: 0px;
      right: 0px;
      z-index: 999;
    }
  }
  #iframewrapper {
    width: 50%;
    height: 100%;
  }
}
</style>
<style lang="scss">
.vue-codemirror {
  height: 33.33%;
  .CodeMirror {
    height: 100%;
  }
}
#iframeResult {
  height: 100%;
  width: 100%;
}
</style>
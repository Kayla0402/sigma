<template>
  <div>
    <codemirror
      ref="cm"
      v-model="code"
      :options="cmOptions"
      @input="inputChange"
    ></codemirror>
  </div>
</template>

<script>
// 全局引入vue-codemirror
import { codemirror } from "vue-codemirror";
// 引入css文件
import "codemirror/lib/codemirror.css";
// 引入主题 可以从 codemirror/theme/ 下引入多个
import "codemirror/theme/idea.css";
// 引入语言模式 可以从 codemirror/mode/ 下引入多个
import "codemirror/mode/sql/sql.js";

// 搜索功能
// find：Ctrl-F (PC), Cmd-F (Mac)
// findNext：Ctrl-G (PC), Cmd-G (Mac)
// findPrev：Shift-Ctrl-G (PC), Shift-Cmd-G (Mac)
// replace：Shift-Ctrl-F (PC), Cmd-Alt-F (Mac)
// replaceAll：Shift-Ctrl-R (PC), Shift-Cmd-Alt-F (Mac)
import "codemirror/addon/dialog/dialog.css";
import "codemirror/addon/dialog/dialog";
import "codemirror/addon/search/searchcursor";
import "codemirror/addon/search/search";
import "codemirror/addon/search/jump-to-line";
import "codemirror/addon/search/matchesonscrollbar";
import "codemirror/addon/search/match-highlighter";

// 代码提示功能 具体语言可以从 codemirror/addon/hint/ 下引入多个
import "codemirror/addon/hint/show-hint.css";
import "codemirror/addon/hint/show-hint";
import "codemirror/addon/hint/sql-hint";

// 高亮行功能
import "codemirror/addon/selection/active-line";
import "codemirror/addon/selection/selection-pointer";

// 调整scrollbar样式功能
import "codemirror/addon/scroll/simplescrollbars.css";
import "codemirror/addon/scroll/simplescrollbars";

// 自动括号匹配功能
import "codemirror/addon/edit/matchbrackets";

// 全屏功能 由于项目复杂，自带的全屏功能一般不好使
import "codemirror/addon/display/fullscreen.css";
import "codemirror/addon/display/fullscreen";

// 显示自动刷新
import "codemirror/addon/display/autorefresh";

// 多语言支持？
import "codemirror/addon/mode/overlay";
import "codemirror/addon/mode/multiplex";

// 代码段折叠功能
import "codemirror/addon/fold/foldcode";
import "codemirror/addon/fold/foldgutter";
import "codemirror/addon/fold/foldgutter.css";

import "codemirror/addon/fold/brace-fold";
import "codemirror/addon/fold/comment-fold";
import "codemirror/addon/fold/xml-fold.js";
import "codemirror/addon/fold/indent-fold.js";
import "codemirror/addon/fold/markdown-fold.js";
import "codemirror/addon/fold/comment-fold.js";

// merge功能
import "codemirror/addon/merge/merge.css";
import "codemirror/addon/merge/merge";
// google DiffMatchPatch
import DiffMatchPatch from "diff-match-patch";
// DiffMatchPatch config with global
window.diff_match_patch = DiffMatchPatch;
window.DIFF_DELETE = -1;
window.DIFF_INSERT = 1;
window.DIFF_EQUAL = 0;

export default {
  name: "Show",
  components: { codemirror },
  data() {
    return {
      code: "select a from table1 where b = 1",
      cmOptions: {
        // 语言及语法模式
        mode: "text/x-sql",
        // 主题
        theme: "idea",
        // 显示函数
        line: true,
        lineNumbers: true,
        // 软换行
        lineWrapping: true,
        // tab宽度
        tabSize: 4,
        // 代码提示功能
        hintOptions: {
          // 避免由于提示列表只有一个提示信息时，自动填充
          completeSingle: false,
          // 不同的语言支持从配置中读取自定义配置 sql语言允许配置表和字段信息，用于代码提示
          tables: {
            table1: ["c1", "c2"],
          },
          // hint: this.handleShowHint2,
        },
        // 高亮行功能
        styleActiveLine: true,
        // 调整scrollbar样式功能
        scrollbarStyle: "overlay",
        // 自动括号匹配功能
        matchBrackets: true,
      },
    };
  },
  methods: {
    inputChange() {
      this.$nextTick(() => {
        // console.log("code:" + this.code);
        // console.log("content:" + content);
      });
    },
    /**

使用自定义hint，网上没有详细的讲解，这里着重讲一下。

1. 第一个入参cmInstance指的是codeMirror实例，第二个是配置中的的hintOptions值。
2. 从cmInstance中getCursor指的是获取光标实例，光标实例里有行数、列数。
3. 可以从cmInstance的getLine方法里传入一个行数，从而获取行中的字符串。
4. token对象是cmInstance对光标所在字符串进行提取处理，从对应语言的类库中判断光标所在字符串的类型，
  方便hint提示。token中包含start、end、string、type等属性，
  start和end指的是光标所在字符串在这一行的起始位置和结束位置，string是提取的字符串，
  type表示该字符串是什么类型（keyword/operator/string等等不定）
5. 下面方法中返回的结果体意思是：下拉列表中展示hello和world两行提示，
  from和to表示当用户选择了提示内容后，这些提示内容要替换编辑区域的哪个字符串。方法中的代码含义是替换token全部字符串。

*/
    handleShowHint(cmInstance, hintOptions) {
      let cursor = cmInstance.getCursor();
      // let cursorLine = cmInstance.getLine(cursor.line);
      // let end = cursor.ch;
      // let start = end;

      let token = cmInstance.getTokenAt(cursor);
      // console.log(cmInstance, cursor, cursorLine, end, token)
      console.log(hintOptions);
      // console.log(start)
      // return hintOptions.tables;
      return {
        list: ["hello", "world"],
        from: { ch: token.start, line: cursor.line },
        to: { ch: token.end, line: cursor.line },
      };
    },
    handleShowHint2(cmInstance, hintOptions) {
      let cursor = cmInstance.getCursor();
      let cursorLine = cmInstance.getLine(cursor.line);
      let end = cursor.ch;
      // let start = end;
      console.log(hintOptions);

      let token = cmInstance.getTokenAt(cursor);
      console.log(cmInstance, cursor, cursorLine, end, token);
      return {
        list: [
          {
            text: "hello",
            displayText: "你好呀",
            displayInfo: "提示信息1",
            render: this.hintRender,
          },
          {
            text: "world",
            displayText: "世界",
            displayInfo: "提示信息2",
            render: this.hintRender,
          },
        ],
        from: {
          ch: token.start,
          line: cursor.line,
        },
        to: {
          ch: token.end,
          line: cursor.line,
        },
      };
    },
    hintRender(element, self, data) {
      let div = document.createElement("div");
      div.setAttribute("class", "autocomplete-div");

      let divText = document.createElement("div");
      divText.setAttribute("class", "autocomplete-name");
      divText.innerText = data.displayText;

      let divInfo = document.createElement("div");
      divInfo.setAttribute("class", "autocomplete-hint");
      divInfo.innerText = data.displayInfo;

      div.appendChild(divText);
      div.appendChild(divInfo);
      element.appendChild(div);
    },
  },
  mounted() {
    // 代码提示功能 当用户有输入时，显示提示信息
    this.$refs.cm.codemirror.on("inputRead", (cm) => {
      cm.showHint();
    });
    this.$refs.cm.codemirror.setSize(
      "auto",
      document.documentElement.clientHeight - 200 + "px"
    );
    this.$nextTick(() => {
      window.addEventListener("resize", () => {
        //监听浏览器窗口大小改变
        //浏览器变化执行动作
        this.$refs.cm.codemirror.setSize(
          "auto",
          document.documentElement.clientHeight - 200 + "px"
        );
      });
    });
    // this.$refs.cm.codemirror.setOption("readOnly", true);
    // // 不设的话，默认是530
    // this.$refs.cm.codemirror.setOption("cursorBlinkRate", -1);
  },
};
</script>
<style>
.autocomplete-div {
  display: inline-block;
  width: 100%;
}
.autocomplete-name {
  display: inline-block;
}
.autocomplete-hint {
  display: inline-block;
  float: right;
  color: #0088ff;
  margin-left: 1em;
}
</style>

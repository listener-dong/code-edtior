<!-- 组件 -->
<template>
  <div class="content">
    <input type="text" v-model="searchKeyword" @input="searchCode" placeholder="Search keyword">
    <codemirror v-model="code" :options="options" class="code-mirror" ref="codemirror" :style="{ height: height + 'px' }">
    </codemirror>
  </div>
</template>

<script>
// 文件内引入
import { codemirror } from "vue-codemirror";
// 引入样式、主题、代码风格等配置或样式文件
import "codemirror/lib/codemirror.css";
import 'codemirror/theme/blackboard.css';
import 'codemirror/mode/yaml/yaml.js'
export default {
  // 注册使用
  components: {
    codemirror,
  },
  props: {
    // 是否只读
    readOnly: { type: Boolean, default: false },
    // mark 标记背景颜色
    markColor: { type: String, default: 'red' },
    // 内容
    value: { type: String, default: `` },
    // 高度
    height: { type: Number, default: 300 },
    // 代码类型
    mode: { type: String, default: 'yaml' }
  },
  data() {
    return {
      searchKeyword: '',
      code: '',
      options: {
        line: true,
        theme: "blackboard", // 主题
        tabSize: 4, // 制表符的宽度
        indentUnit: 2, // 一个块应该缩进多少个空格（无论这在编辑语言中意味着什么）。默认值为 2。
        firstLineNumber: 1, // 从哪个数字开始计算行数。默认值为 1。
        readOnly: this.readOnly, // 只读
        autorefresh: true,
        smartIndent: true, // 上下文缩进
        lineNumbers: true, // 是否显示行号
        styleActiveLine: true, // 高亮选中行
        viewportMargin: Infinity, //处理高度自适应时搭配使用
        showCursorWhenSelecting: true, // 当选择处于活动状态时是否应绘制游标
        mode: this.mode, // 代码类型
      },
      mark_all: []
    };
  },
  created() {
    this.code = this.value
  },
  watch: {
    code(val) {
      this.$emit('update:value', val);
    }
  },
  methods: {
    searchCode() {
      // 获取编辑器实例
      const editor = this.$refs.codemirror.codemirror;

      // 要搜索的关键词
      this.getMatchedLineNumbers(this.searchKeyword, editor)
    },
    // 在 CodeMirror 编辑器中获取匹配关键词的行号和位置，并进行标记
    getMatchedLineNumbers(keyword, cm) {
      const content = cm.getValue(); // 获取编辑器中的全部内容
      const regex = new RegExp(keyword); // 创建正则表达式，用于匹配关键词

      this.clearPreviousMarks(); // 清除之前的标记

      // 将内容按行分割并遍历每一行
      content.split('\n').forEach((line, index) => {
        const match = line.match(regex); // 在当前行中查找关键词的位置
        if (match) { // 如果找到了匹配的关键词
          // 在匹配的位置添加标记
          const mark = cm.markText(
            { line: index, ch: match.index }, // 标记起始位置
            { line: index, ch: match.index + keyword.length }, // 标记结束位置
            { css: `background-color: ${this.markColor}` } // 标记样式
          );
          this.mark_all.push(mark); // 存储标记实例
        }
      });
    },

    // 清除之前的标记
    clearPreviousMarks() {
      this.mark_all.forEach(mark => mark.clear()); // 清除之前的标记
      this.mark_all = []; // 重置标记数组
    }

  }
};
</script>

<style>
.code-mirror {
  font-size: 14px;
  line-height: 24px;
}

.CodeMirror-line {
  margin-left: 12px !important;
}

.highlight {
  background-color: yellow;
}

.CodeMirror {
  height: 100% !important;
}
</style>
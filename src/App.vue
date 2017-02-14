<template>
  <div id="app">
    <styleEditor ref="styleEditor" :code="current"></styleEditor>
    <resumeEditor ref="resumeEditor" :markdown="currentResume" :enableHtml="enableHtml"></resumeEditor>
  </div>
</template>

<script>
  import styleEditor from './components/styleEditor'
  import resumeEditor from './components/resumeEditor'
  import marked from 'marked'

  export default {
    components: {
      styleEditor,
      resumeEditor
    },
    data() {
      return {
        style: [
        `/*
          * Inspired by http://strml.net/
          * 大家好，我是方方 
          * 二月了，好多公司都在招聘，你是不是也在准备简历呀。
          * 说做就做，我也来写一份简历！
          */

          /* 首先给所有元素加上过渡效果 */
          * {
            transition: all .3s;
          }
          /* 白色背景太单调了，我们来点背景 */
          html {
            color: rgb(222,222,222); background: rgb(0,43,54);
          }
          /* 文字离边框太近了 */
          .styleEditor {
            padding: .5em;
            border: 1px solid;
            margin: .5em;
            overflow: auto;
            width: 45vw; height: 90vh;
          }
          /* 代码高亮 */
          .token.selector{ color: rgb(133,153,0); }
          .token.property{ color: rgb(187,137,0); }
          .token.punctuation{ color: yellow; }
          .token.function{ color: rgb(42,161,152); }

          /* 加点 3D 效果呗 */
          html{
            perspective: 1000px;
            min-height: 100vh;
          }
          .styleEditor {
            position: fixed; left: 0; top: 0; 
            transition: none;
            transform: rotateY(10deg) translateZ(-100px) ;
          }

          /* 接下来我给自己准备一个编辑器 */
          .resumeEditor{
            position: fixed; right: 0; top: 0;
            padding: .5em;  margin: .5em;
            width: 48vw; height: 90vh; 
            border: 1px solid;
            background: white; color: #222;
            overflow: auto;
          }
          /* 好了，我开始写简历了 */


          `,
                    `
          /* 这个简历好像差点什么
           * 对了，这是 Markdown 格式的，我需要变成对 HR 更友好的格式
           * 简单，用开源工具翻译成 HTML 就行了
           */
          `
                    ,
                    `
          /* 再对 HTML 加点样式 */
          .resumeEditor{
            padding: 2em;
          }
          .resumeEditor h2{
            display: inline-block;
            border-bottom: 1px solid;
            margin: 1em 0 .5em;
          }
          .resumeEditor ul,.resumeEditor ol{
            list-style: none;
          }
          .resumeEditor ul> li::before{
            content: '•';
            margin-right: .5em;
          }
          .resumeEditor ol {
            counter-reset: section;
          }
          .resumeEditor ol li::before {
            counter-increment: section;            
            content: counters(section, ".") " ";  
            margin-right: .5em;
          }
          .resumeEditor blockquote {
            margin: 1em;
            padding: .5em;
            background: #ddd;
          }
        `],
        current: '',
        interval: 50,
        resume: `方应杭
----

资深前端工程师，资深前端讲师，现在在 [饥人谷](http://jirengu.com) 教前端课程。

技能
----

* 前端开发
* Rails 开发
* Node.js 开发
* 前端授课

工作经历
----

1. [饥人谷](http://jirengu.com)
2. 腾讯即时通讯平台部
3. 阿里巴巴B2B部门
4. 彩程知人项目组

链接
----

* [GitHub](https://github.com/frankfang)
* [我的文章](https://www.zhihu.com/people/zhihusucks/pins/posts)

> 如果你喜欢这个效果，Fork [我的项目](https://github.com/jirengu-inc/animating-resume)，打造你自己的简历！

`,
        currentResume: '',
        enableHtml: false
      }
    },
    methods: {
      show(n) {
        return new Promise((resolve, reject) => {
          let interval = this.interval
          let show = () => {
            let style = this.style[n]
            if(this.current.length < style.length) {
              this.current = style.substring(0, this.current.length + 1)
              if(this.current.substring(this.current.length - 1, this.current.length) === '\n') {
                this.$nextTick(() => {
                  this.$refs.styleEditor.goBottom()
                })
              }
              setTimeout(show, interval)
            } else {
              resolve()
            }
          }
          show()
        })
      },
      showHtml() {
        return new Promise((resolve, reject) => {
          this.enableHtml = true
          resolve()
        })
      },
      showResume() {
        return new Promise((resolve, reject) => {
          let length = this.resume.length
          let interval = this.interval
          let showResume = () => {
            if(this.currentResume.length < length) {
              this.currentResume = this.resume.substring(0, this.currentResume.length + 1)
              let lastChar = this.currentResume[this.currentResume.length - 1]
              let prevChar = this.currentResume[this.currentResume.length - 2]
              if (prevChar === '\n' && this.$refs.resumeEditor) {
                this.$nextTick(() => this.$refs.resumeEditor.goBottom())
              }
              setTimeout(showResume, interval)
            } else {
              resolve()
            }
          }
          showResume()
        })
      },
      makeResume: async function() {
        await this.show(0)
        await this.showResume()
        await this.show(1)
        await this.showHtml()
        await this.show(2)
      }
    },
    created() {
      this.makeResume()
    }
  }
</script>

<style scoped>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }
</style>
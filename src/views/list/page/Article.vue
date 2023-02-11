<template>
  <div>
    <div ref="editor"></div>
  </div>
</template>

<script>
import E from 'wangeditor-antd'
import { ArticleListContent } from '@/components'
import IconText from '@/views/list/search/components/IconText'

export default {
  name: 'Article',
  components: {
    IconText,
    ArticleListContent,
  },
  data() {
    return {
      loading: true,
      loadingMore: false,
      data: [],

      content: ' ', // 默认清空得空一格
      getFullText: '', // 还没搞明白 不过可以先忽略 好像没用
    }
  },
  mounted() {
    this.getEditor()
  },
  methods: {
    loadMore () {
      this.loadingMore = true
      this.$http.get('/list/article').then(res => {
        this.data = this.data.concat(res.result)
      }).finally(() => {
        this.loadingMore = false
      })
    },
    getEditor() {
      const editor = new E(this.$refs.editor)
      editor.customConfig.onchange = (html) => {
        this.getFullText(html) // 内容赋值
      }
      editor.customConfig.uploadImgServer = '你的上传地址'
      editor.customConfig.uploadFileName = 'file'
      // 新增
      editor.customConfig.zIndex = 100
      // 工具条高度 默认 50px small为40px
      editor.customConfig.toolBarSize = 'small'
      // 最小高度 默认 100px
      editor.customConfig.minHeight = '400px'
      // 最大高度 默认 500px
      editor.customConfig.maxHeight = '600px'
      editor.customConfig.menus = [
        // 缺什么在官网搜名字一加就行
        'head', // 标题
        'bold', // 粗体
        'fontSize', // 字号
        'foreColor', // 文字颜色
        'backColor', // 背景颜色
        'link', // 插入链接
        'list', // 列表
        'image', // 插入图片
      ]
      editor.customConfig.uploadImgParams = {
        from: 'editor',
      }
      editor.create()
      // 如果默认传递content值则渲染默认内容
      if (this.content) {
        editor.txt.html(this.content)
      }
    },
  },
}
</script>

<style scoped>
</style>

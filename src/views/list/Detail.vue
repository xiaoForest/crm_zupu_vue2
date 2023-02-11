<template>
  <page-header-wrapper
    title="家族名称哦哈哈哈哈唯一的"
    class="detail"
    :tab-list="tabList"
    :tab-active-key="tabActiveKey"
    :tab-change="
      (key) => {
        this.tabActiveKey = key
      }
    "
  >
    <template v-slot:content>
      <a-descriptions size="small" :column="isMobile ? 1 : 2">
        <a-descriptions-item label="创建人员">曲丽丽</a-descriptions-item>
        <a-descriptions-item label="访问模式">公开</a-descriptions-item>
        <a-descriptions-item label="族谱编号">GOOD0001</a-descriptions-item>
        <a-descriptions-item label="访问人次">
          <a href="">12421</a>
        </a-descriptions-item>
        <a-descriptions-item label="创建日期">2017-07-07</a-descriptions-item>
        <a-descriptions-item label="成员人数">
          <a href="">12421</a>
        </a-descriptions-item>
      </a-descriptions>
    </template>

    <!-- actions -->
    <template v-slot:extra>
      <a-button-group style="margin-right: 5px">
        <a-button>冻结族谱</a-button>
        <a-dropdown-button @click="handleButtonClick">
          操作二
          <template #overlay>
            <a-menu @click="handleMenuClick">
              <a-menu-item key="1">
                <UserOutlined />
                1st menu item
              </a-menu-item>
              <a-menu-item key="2">
                <UserOutlined />
                2nd menu item
              </a-menu-item>
              <a-menu-item key="3">
                <UserOutlined />
                3rd item
              </a-menu-item>
            </a-menu>
          </template>
        </a-dropdown-button>
      </a-button-group>
      <a-button type="primary" style="color: #fff; margin-right: 4px">主操作</a-button>
      <a-button>
        <router-link :to="`/list/basic-list`">成员列表</router-link>
      </a-button>
    </template>

    <template v-slot:extraContent>
      <a-row class="status-list">
        <a-col :xs="12" :sm="12">
          <div class="text">会员</div>
          <div class="heading">未开通</div>
        </a-col>
        <a-col :xs="12" :sm="12">
          <div class="text">订单金额</div>
          <div class="heading">生效</div>
        </a-col>
      </a-row>
    </template>

    <div v-show="tabActiveKey == 'tab1'">
      <div ref="editor"></div>
      <!-- <div class="text-editor">
        <p>纯文本</p>
      </div> -->
    </div>
    <div v-show="tabActiveKey == 'tab2'">
      <div class="ant-pro-pages-list-projects-cardList">
        <a-list :loading="loading" :data-source="data" :grid="{ gutter: 24, xl: 4, lg: 3, md: 3, sm: 2, xs: 1 }">
          <a-list-item slot="renderItem" slot-scope="item">
            <a-card class="ant-pro-pages-list-projects-card" hoverable>
              <img slot="cover" :src="item.cover" :alt="item.title" />
              <a-card-meta :title="item.title">
                <template slot="description">
                  <ellipsis :length="50">{{ item.description }}</ellipsis>
                </template>
              </a-card-meta>
              <div class="cardItemContent">
                <span>{{ item.updatedAt | fromNow }}</span>
                <div class="avatarList">
                  <avatar-list size="small" :max-length="2">
                    <avatar-list-item
                      v-for="(member, i) in item.members"
                      :key="`${item.id}-avatar-${i}`"
                      :src="member.avatar"
                      :tips="member.name"
                    />
                  </avatar-list>
                </div>
              </div>
            </a-card>
          </a-list-item>
        </a-list>
      </div>
    </div>
  </page-header-wrapper>
</template>

<script>
// https://top-dante.github.io/wangeditor-antd/index.html
import E from 'wangeditor-antd'
import moment from 'moment'
import { TagSelect, StandardFormRow, Ellipsis, AvatarList } from '@/components'

const TagSelectOption = TagSelect.Option
const AvatarListItem = AvatarList.Item
const dataSource = []
dataSource.push({})
for (let i = 0; i < 11; i++) {
  dataSource.push({
    id: i,
    title: 'Alipay',
    avatar: 'https://gw.alipayobjects.com/zos/rmsportal/WdGqmHpayyMjiEhcKoVE.png',
    content:
      '在中台产品的研发过程中，会出现不同的设计规范和实现方式，但其中往往存在很多类似的页面和组件，这些类似的组件会被抽离成一套标准规范。',
  })
}

export default {
  components: {
    AvatarList,
    AvatarListItem,
    Ellipsis,
    TagSelect,
    TagSelectOption,
    StandardFormRow,
  },
  // props: ['getFullText', 'content'], // 回调方法 似乎没啥用  那个编辑器官方的示例
  name: 'CardList',
  data() {
    this.tabList = [
      { key: 'tab1', tab: '家族简介' },
      { key: 'tab2', tab: '家族相册' },
    ]
    return {
      tabActiveKey: 'tab1',
      extraImage: 'https://gw.alipayobjects.com/zos/rmsportal/RzwpdLnhmvDJToTdfDPe.png',
      dataSource,
      content: ' ', // 默认清空得空一格
      getFullText: '', // 还没搞明白 不过可以先忽略 好像没用
      data: [],
      form: this.$form.createForm(this),
      loading: true,
    }
  },
  filters: {
    fromNow(date) {
      return moment(date).fromNow()
    },
  },
  mounted() {
    this.getList()
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
  methods: {
    testFun() {
      this.$message.info('快速开始被点击！')
    },
    handleChange(value) {
      console.log(`selected ${value}`)
    },
    getList() {
      // 这里是相册 官方的实例而已
      this.$http.get('/list/article', { params: { count: 8 } }).then((res) => {
        console.log('res', res)
        this.data = res.result
        this.loading = false
      })
    },
  },
}
</script>

<style lang="less" scoped>
@import '~@/components/index.less';

.card-list {
  :deep(.ant-card-body:hover) {
    .ant-card-meta-title > a {
      color: @primary-color;
    }
  }

  :deep(.ant-card-meta-title) {
    margin-bottom: 12px;

    & > a {
      display: inline-block;
      max-width: 100%;
      color: rgba(0, 0, 0, 0.85);
    }
  }

  :deep(.meta-content) {
    position: relative;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    height: 64px;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;

    margin-bottom: 1em;
  }
}

.card-avatar {
  width: 48px;
  height: 48px;
  border-radius: 48px;
}

.ant-card-actions {
  background: #f7f9fa;

  li {
    float: left;
    text-align: center;
    margin: 12px 0;
    color: rgba(0, 0, 0, 0.45);
    width: 50%;

    &:not(:last-child) {
      border-right: 1px solid #e8e8e8;
    }

    a {
      color: rgba(0, 0, 0, 0.45);
      line-height: 22px;
      display: inline-block;
      width: 100%;
      &:hover {
        color: @primary-color;
      }
    }
  }
}

.new-btn {
  background-color: #fff;
  border-radius: 2px;
  width: 100%;
  height: 188px;
}

.ant-pro-components-tag-select {
  :deep(.ant-pro-tag-select .ant-tag) {
    margin-right: 24px;
    padding: 0 8px;
    font-size: 14px;
  }
}
.ant-pro-pages-list-projects-cardList {
  margin-top: 24px;

  :deep(.ant-card-meta-title) {
    margin-bottom: 4px;
  }
  :deep(.ant-card-meta-description) {
    height: 44px;
    overflow: hidden;
    line-height: 22px;
  }

  .cardItemContent {
    display: flex;
    height: 20px;
    margin-top: 16px;
    margin-bottom: -4px;
    line-height: 20px;

    > span {
      flex: 1 1;
      color: rgba(0, 0, 0, 0.45);
      font-size: 12px;
    }

    :deep(.ant-pro-avatar-list) {
      flex: 0 1 auto;
    }
  }
}

.text {
  color: rgba(0, 0, 0, 0.45);
}

.heading {
  color: rgba(0, 0, 0, 0.85);
  font-size: 20px;
}
</style>

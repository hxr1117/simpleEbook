<template>
  <div class="ebook">
    <title-bar :ifTitleAndMenuShow="ifTitleAndMenuShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggle"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar
      :ifTitleAndMenuShow="ifTitleAndMenuShow"
      :fontSizeList="fontSizeList"
      :defaultFontSize="defaultFontSize"
      ref="menuBar"
      @setFontSize="setFontSize"
      :themeList="themeList"
      @setTheme="setTheme"
      :defaultTheme="defaultTheme"
      @onProgressChange="onProgressChange"
      :bookAvailable="bookAvailable"
      @jumpTo="jumpTo"
      :navigation="navigation"
    ></menu-bar>
  </div>
</template>

<script>
import TitleBar from './components/TitleBar'
import MenuBar from './components/MenuBar'
import Epub from 'epubjs'
// const DOWNLOAD_URL = 'static/1.epub'
global.ePub = Epub
export default {
  components: {
    TitleBar,
    MenuBar
  },
  data() {
    return {
      ifTitleAndMenuShow: false,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 }
      ],
      defaultFontSize: 16,
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              color: '#000',
              background: '#fff'
            }
          }
        },
        {
          name: 'eye',
          style: {
            body: {
              color: '#000',
              background: '#ceeaba'
            }
          }
        },
        {
          name: 'night',
          style: {
            body: {
              color: '#ddd',
              background: '#000'
            }
          }
        },
        {
          name: 'gold',
          style: {
            body: {
              color: '#000',
              background: 'rgb(241, 236, 226)'
            }
          }
        }
      ],
      // 保存上一次选择主题的index
      defaultTheme: 0,
      bookAvailable: false
    }
  },
  methods: {
    showEpub() {
      const url = '/static/1.epub'
      // 'http://192.168.31.71:8081/epub/Laws/2015_Book_ProtectingTheRightsOfPeopleWit.epub'
      this.book = new Epub(url)
      console.log(this.book)
      // id: read
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        height: window.innerHeight,
        method: 'defalut'
      })
      // 通过rendition.display渲染电子书
      this.rendition.display()
      // 获取themes对象
      this.themes = this.rendition.themes
      // 设置默认字体
      this.setFontSize(this.defaultFontSize)
      // themes.register(name, style)
      // themes.select(name)
      this.registerTheme()
      this.setTheme(this.defaultTheme)
      // 获取locations对象
      // 通过epubjs构造函数来实现
      this.book.ready
        .then(() => {
          return this.book.locations.generate()
        })
        .then(() => {
          // 目录
          this.navigation = this.book.navigation
          this.locations = this.book.locations
          this.bookAvailable = true
          // this.onProgressChange(50)
        })
    },
    prevPage() {
      if (this.rendition) {
        this.rendition.prev()
      }
    },
    nextPage() {
      if (this.rendition) {
        this.rendition.next()
      }
    },
    toggle() {
      this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow
      if (!this.ifTitleAndMenuShow) {
        // 利用ref调用子组建的dom的函数
        this.$refs.menuBar.hideSetting()
      }
    },
    // 接受子组建传来的参数
    setFontSize(fontSize) {
      console.log(fontSize)
      this.defaultFontSize = fontSize
      if (this.themes) {
        this.themes.fontSize(fontSize + 'px')
      }
    },
    registerTheme() {
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style)
      })
    },
    setTheme(index) {
      this.themes.select(this.themeList[index].name)
      this.defaultTheme = index
    },
    // progress: 0-100
    onProgressChange(progress) {
      const percentage = progress / 100
      const location =
        percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      this.rendition.display(location)
    },
    jumpTo(href) {
      this.rendition.display(href)
      this.hideTitleAndMenu()
    },
    hideTitleAndMenu() {
      this.ifTitleAndMenuShow = false
      this.$refs.menuBar.hideSetting()
      // 隐藏目录
      this.$refs.menuBar.hideContent()
    }
  },
  mounted() {
    this.showEpub()
  }
}
</script>

<style lang="scss" scoped>
@import 'assets/styles/global';
.ebook {
  position: relative;
  .read-wrapper {
    .mask {
      position: absolute;
      top: 0;
      right: 0;
      width: 100%;
      height: 100%;
      z-index: 100;
      display: flex;
      .left {
        flex: 0 0 px2rem(100);
      }
      .center {
        flex: 1;
      }
      .right {
        flex: 0 0 px2rem(100);
      }
    }
  }
}
.icon {
  color: #333;
  font-size: px2rem(20);
}
</style>

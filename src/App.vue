
<template>
  <div id="wrapper">
    <header>
      <div class="container">
        <div class="title">{{ selectedEvent||'' }} Issue 小精靈</div>
        <p>幫你開 Issue 的小精靈</p>
      </div>
    </header>
    <main class="container" v-if="selectedEvent">
      <div class="box">
        <div class="title">小提醒</div>
        <p>在 Title 或 Description 輸入 #{group} 會自動帶入組別名稱。</p>
      </div>
      <label>Title</label>
      <input v-model="title" @input="updateLinks" />
      <label>Description</label>
      <v-md-editor
        left-toolbar="undo redo | h bold italic strikethrough quote | ul ol table hr | name pancake"
        right-toolbar="preview"
        v-model="description"
        height="400px"
        @change="updateLinks"
        :toolbar="toolbar"
      ></v-md-editor>
      <label>Options</label>
      <div class="options">
        <div class="option">
          <input type="checkbox" v-model="options.autoAssign" @change="updateLinks" id="autoAssign" />
          <label for="autoAssign">自動 Assign 組長</label>
        </div>
        <div class="option">
          <input
            type="checkbox"
            v-model="options.enableRelatedIssue"
            @change="updateLinks"
            id="enableRelatedIssue"
          />
          <label for="enableRelatedIssue">關聯相關 Issue</label>
        </div>
      </div>
      <div v-if="options.enableRelatedIssue">
        <input
          v-model="relatedIssue"
          @input="updateLinks"
          type="number"
          style="max-width: 256px;margin-left: 21px;"
          placeholder="issue id"
        />
      </div>
      <button @click="createIssue" class="magic-button">🪄 來點魔法！</button>
      <p class="text-center">若魔法施展失敗，請檢查瀏覽器是否封鎖了快顯視窗，或直接點擊下方連結。</p>
      <div class="links">
        <a
          v-for="link in links"
          :href="link.href"
          target="_blank"
          :key="link.title"
          v-text="link.title"
        />
      </div>
    </main>
    <main class="container" v-else>
      <div class="box">
        <div class="title">尚未選擇活動</div>
        <p>請先選擇活動。</p>
      </div>
      <div class="links">
        <a
          v-for="event in events"
          :href="`?event=${event.title}`"
          :key="event.title"
          v-text="event.title"
        />
      </div>
    </main>
    <footer>
      Developed by
      <a href="https://gnehs.net" target="_blank">勝勝寶寶</a> | Made with
      <span @click="randomThemeColor">🥞</span> in Taiwan |
      <a href="https://github.com/gnehs/issue-generator" target="_blank">GitHub</a>
    </footer>
  </div>
</template>

<style lang="sass">
:root
  --rosewater: rgb(245, 224, 220)
  --flamingo: rgb(242, 205, 205)
  --pink: rgb(245, 194, 231)
  --mauve: rgb(203, 166, 247)
  --red: rgb(243, 139, 168)
  --maroon: rgb(235, 160, 172)
  --peach: rgb(250, 179, 135)
  --yellow: rgb(249, 226, 175)
  --green: rgb(166, 227, 161)
  --teal: rgb(148, 226, 213)
  --sky: rgb(137, 220, 235)
  --sapphire: rgb(116, 199, 236)
  --blue: rgb(137, 180, 250)
  --lavender: rgb(180, 190, 254)
  --text: rgb(205, 214, 244)
  --subtext1: rgb(186, 194, 222)
  --subtext0: rgb(166, 173, 200)
  --overlay2: rgb(147, 153, 178)
  --overlay1: rgb(127, 132, 156)
  --overlay0: rgb(108, 112, 134)
  --surface2: rgb(88, 91, 112)
  --surface1: rgb(69, 71, 90)
  --surface0: rgb(49, 50, 68)
  --base: rgb(30, 30, 46)
  --mantle: rgb(24, 24, 37)
  --crust: rgb(17, 17, 27)
  --theme-color: var(--rosewater)  // 初始主題色為 Rosewater
  
  --border-color: #ddd
  line-height: 1.5
  accent-color: var(--theme-color)

body, html, .v-md-textarea-editor pre, .v-md-textarea-editor textarea, .vuepress-markdown-body
  font-family: 'Ubuntu Mono', 'Noto Sans TC', sans-serif !important
  background-color: var(--mantle)  // 背景設為 Mantle
  color: var(--text)  // 文字顏色設為 Text

*

  box-sizing: border-box
  transition: all 0.2s ease

a
  color: var(--theme-color)
  &:hover
    opacity: 0.8

#wrapper
  display: flex
  flex-direction: column
  min-height: 100svh
  &>main
    flex: 1

header
  padding: 64px 0
  margin-bottom: 16px
  background-color: var(--surface1)  // 背景色改為 Surface1
  .title
    font-size: 2rem
    color: var(--text)  // 標題顏色為 Text

footer
  font-size: 0.875rem
  margin-top: 32px
  padding: 32px 0
  text-align: center
  color: var(--subtext0)  // 文字顏色為 Subtext0
  background-color: var(--surface1)  // 背景色為 Surface1

label
  display: inline-block
  font-size: 1rem
  margin-top: 16px
  margin-bottom: 8px
  color: var(--theme-color)
  filter: brightness(.75)

input:not([type="checkbox"])
  width: 100%
  padding: 8px 12px
  border: 1px solid var(--border-color)
  border-radius: 4px
  font-size: 1.25rem
  &:focus
    outline: none
    border: 1px solid var(--theme-color)

input[type="checkbox"]
  accent-color: var(--theme-color)
  margin-right: 8px

p
  font-size: 1rem
  opacity: 0.5
  margin: 0

.text-center
  text-align: center

.container
  width: min(calc(100vw - 40px),960px)
  margin: 0 auto
  font-size: 16px

.title
  font-size: 1.25rem
  font-weight: bold
  color: var(--theme-color)
  margin-bottom: 8px

.box
  padding: 16px 24px
  margin-bottom: 16px
  border-left: 4px solid var(--theme-color)
  background-color: var(--surface2)  // 背景色設為 Surface2
  border-radius: 0 16px 16px 0
  p
    color: var(--theme-color)
    filter: brightness(0.2)

.links
  display: flex
  flex-wrap: wrap
  justify-content: center
  align-items: center
  gap: 8px
  margin-top: 4px
  a
    background-color: var(--theme-color)
    color: #fff
    padding: 4px 8px
    border-radius: 8px
    text-decoration: none
    &:hover
      box-shadow: 0 10px 20px -10px rgba(var(--theme-color),1)
      transform: translateY(-3px)
    &:active
      box-shadow: 0 5px 10px -5px rgba(var(--theme-color),1)
      transform: translateY(-1px)

.magic-button
  background-color: var(--theme-color)
  color: #fff
  font-size: 2rem
  display: block
  margin: 8px auto
  margin-top: 32px
  border: none
  border-radius: 8px
  padding: .5em .75em
  cursor: pointer
  &:hover
    box-shadow: 0 10px 20px -10px rgba(var(--theme-color),1)
    transform: translateY(-3px)
  &:active
    box-shadow: 0 5px 10px -5px rgba(var(--theme-color),1)
    transform: translateY(-1px)

.v-md-editor
  box-shadow: none !important
  border: 1px solid var(--border-color)

.v-pancake:before
  content: "🥞"

.v-name:before
  content: "📛"

.v-md-editor__toolbar-item
  color: var(--theme-color)

.vuepress-markdown-body
  color: var(--text) !important
  padding: 20px 20px 30px !important
  font-size: 14px !important
  p
    font-size: 14px !important
    opacity: 1
  a
    color: var(--theme-color) !important
</style>

<script>
import events from './assets/events'

export default {
  data() {
    return {
      title: '[#{group}] 填寫蓬蓬鬆餅預約表單',
      description: '請#{group}組協助填寫蓬蓬鬆餅預約表單。\n\n[傳送門](https://pancake.tw/)',
      relatedIssue: null,
      selectedEvent: null,
      events,
      options: {
        autoAssign: true,
        enableRelatedIssue: false,
      },
      links: [],
      toolbar: {
        pancake: {
          icon: 'v-pancake',
          title: '插入蓬蓬鬆餅',
          action(editor) {
            editor.insert(function (selected) {
              return {
                text: `🥞`,
              };
            });
          },
        },
        name: {
          icon: 'v-name',
          title: '插入組別名稱',
          action(editor) {
            editor.insert(function (selected) {
              return {
                text: `#{group}`,
                selected: `#{group}`,
              };
            });
          },
        },
      },
    }
  },
  mounted() {
    this.randomThemeColor()
    this.selectedEvent = new URLSearchParams(window.location.search).get('event') || null
    this.updateLinks()
  },
  methods: {
    updateLinks() {
      this.links = []
      let event = this.events.find(event => event.title === this.selectedEvent)
      for (let [group, assignUsers] of Object.entries(event ? event.assignments : {})) {
        let hasAssignUser = assignUsers.length
        this.links.push({
          group,
          assignUsers: hasAssignUser ? assignUsers : '待指定',
        })
      }
    },
    randomThemeColor() {
      const colors = [
        '--rosewater', '--flamingo', '--pink', '--mauve', '--red', '--maroon',
        '--peach', '--yellow', '--green', '--teal', '--sky', '--sapphire', '--blue', '--lavender'
      ]
      const randomColor = colors[Math.floor(Math.random() * colors.length)]
      document.documentElement.style.setProperty('--theme-color', `var(${randomColor})`)
    }
  }
}
</script>


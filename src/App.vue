<template>
  <a-layout class="main-app">
    <a-layout-content class="frame-wrapper">
      <!-- 匹配基座自身路由 -->
      <router-view v-show="$route.name" />
      <!-- 匹配微应用 -->
      <div v-show="!$route.name" id="frame"></div>
    </a-layout-content>
  </a-layout>
</template>

<script>
import { mapState } from 'vuex'

import apps from './micro/apps'

const HIDE_HEADER_PATHS = ['/account/login', '/error/404']

const mainMenus = [
  {
    title: '首页',
    path: '/',
  },
]

export default {
  name: 'app',

  components: {},

  data() {
    return {
      defaultActive: '',
    }
  },

  computed: {
    ...mapState({
      isShowAppHeader: (state) => state.app.isShowAppHeader,
    }),

    menus() {
      return [...mainMenus, ...apps]
    },
  },

  watch: {
    $route(route) {
      const next = this.menus.filter((m) => {
        const isMatched = new RegExp(`^${m.path}`).test(route.path)
        return isMatched
      })
      const current = next[next.length - 1]
      this.defaultActive = (current || { title: '' }).title

      this.$store.commit(
        'SET_IS_SHOW_APP_HEADER',
        !HIDE_HEADER_PATHS.includes(route.path)
      )
    },
  },

  mounted() {},

  methods: {
    onMenuSelect(index) {
      if (this.defaultActive === index) return

      const next = this.menus.find((m) => m.title === index)
      this.$router.push({ path: next.path })
    },
  },
}
</script>

<style lang="less" scoped>
@import './styles/themes/default';

.main-app {
  display: flex;
  flex-direction: column;
  height: 100vh;

  font-family: -apple-system, BlinkMacSystemFont, Segoe UI, PingFang SC,
    Hiragino Sans GB, Microsoft YaHei, Helvetica Neue, Helvetica, Arial,
    sans-serif, Apple Color Emoji, Segoe UI Emoji, Segoe UI Symbol;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.menu-wrapper,
.frame-wrapper {
  padding: 0;
}

.menu-wrapper {
  flex: 0 0 auto;
  display: flex;
  align-items: center;
  padding: 0 15px;
  background-color: @brand-primary;
}

.menu {
  flex: 1;
}

.frame-wrapper {
  flex: 1;
}

#frame {
  height: 100%;

  /deep/ div[data-name] {
    height: 100%;
  }
}

.header-logo {
  height: 23px;
  width: 110px;
  margin-right: 60px;
}
</style>

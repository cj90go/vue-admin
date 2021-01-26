<template>
  <el-scrollbar style="overflow-x: auto">
    <el-menu
      class="el-menu-demo"
      mode="horizontal"
      :router="true"
      @select="handleSelect"
    >
      <el-menu-item v-for="(item,index) in permission_routes" :key="index" :index="item.path">
        <span>{{ item.name }}</span>
      </el-menu-item>
    </el-menu>
  </el-scrollbar>
</template>

<script>
import { generateTitle } from '@/utils/i18n'
import { mapGetters } from 'vuex'

export default {
  data() {
    return {
      levelList: null
    }
  },
  computed: {
    ...mapGetters([
      'permission_routes'
    ])
  },
  activeIndex() {
    const route = this.$route
    const { meta, path } = route
    // if set path, the sidebar will highlight the path you set
    console.log(meta, path)
    if (meta.activeMenu) {
      return meta.activeMenu
    }
    return path
  },
  watch: {
    $route: function(to, from) {
      console.log('路由改变了。。。')
      debugger
      this.fetchNavData()
    }
  },
  created() {
    this.fetchNavData()
  },
  methods: {
    fetchNavData() {
      // 初始化菜单激活项
      const cur_path = this.$route.path // 获取当前路由
      const routers = this.$store.getters.permission_routes // 获取路由对象
      let nav_type = ''; let nav_name = ''
      debugger
      for (var i = 0; i < routers.length; i++) {
        const children = routers[i].children
        if (children) {
          for (let j = 0; j < children.length; j++) {
            if (children[j].path === cur_path) {
              nav_type = routers[i].type
              nav_name = routers[i].name
              break
            }
            // 如果该菜单下还有子菜单
            if (children[j].children) {
              const grandChildren = children[j].children
              for (let z = 0; z < grandChildren.length; z++) {
                if (grandChildren[z].path === cur_path) {
                  nav_type = routers[i].type
                  nav_name = routers[i].name
                  break
                }
              }
            }
          }
        }
      }
      this.$store.state.topNavState = nav_type
      this.$store.state.leftNavState = nav_name

      console.log(this.$store.state.leftNavState)
      console.log(this.$store.state.topNavState)

      this.defaultActiveIndex = '/' + nav_name + 'Manager'
      // if (nav_type == "home") {
      //   this.defaultActiveIndex = "/"
      // } else {
      //   this.defaultActiveIndex = "/" + nav_name + "Manager"
      // }
    },
    handleSelect(index) {
      this.defaultActiveIndex = index
      console.log('handleSelect ' + this.defaultActiveIndex)
    },
    generateTitle
  }
}
</script>

<style lang="scss" scoped>

</style>

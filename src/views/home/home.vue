<template>
  <div class="home">
    <HomeBanner />
    <HomeContent
      :contentList="contentList"
      @onMoreClick="onMoreClick"
    />
    <MessageDialog v-if="isShowDialog" msg="没有更多文章了"/>
  </div>
</template>

<script>
  import HomeBanner from '../../components/home/HomeBanner'
  import HomeContent from '../../components/home/HomeContent'
  import MessageDialog from '../../components/base/MessageDialog'
  import { getContentList } from '../../api'
  import { throttle } from '../../utils/util'

  export default {
    name: 'home',
    components: {
      HomeBanner,
      HomeContent,
      MessageDialog
    },
    data () {
      return {
        contentList: [],
        page: 1,
        isShowDialog: false,
        scroll: 0 // 记录滚动高度
      }
    },
    // watch: {
    //   contentList () {
    //     if (this.contentList.length > 0) {
    //       setTimeout(function () {
    //         this.isShowLoading = false
    //         console.log(this.isShowLoading)
    //       }, 500)
    //     }
    //   }
    // },
    mounted () {
      this.$nextTick(() => {
        this.getContentList()
        window.addEventListener('scroll', throttle(this.onScroll, 1000))
      })
    },
    beforeRouteEnter (to, from, next) {
      next(vm => {
        window.scrollTo(0, vm.$store.state.scrollTop)
      })
    },
    // 组件销毁后移除监听事件
    deactivated () {
      window.removeEventListener('scroll', throttle(this.onScroll, 1000))
    },
    beforeRouteLeave (to, from, next) {
      this.$store.commit('changeScrollTop', document.documentElement.scrollTop)
      to.meta.keepAlive = false
      if (to.path !== '/detail') {
        this.$store.commit('changeScrollTop', 0)
      }
      next()
    },
    methods: {
      // 滚动加载
      onScroll () {
        const scrollTop = document.documentElement.scrollTop || document.body.scrollTop
        const clientHeight = document.documentElement.clientHeight || document.body.clientHeight
        const scrollHeight = document.documentElement.scrollHeight || document.body.scrollHeight
        this.scroll = document.documentElement.scrollTop || document.body.scrollTop
        if (clientHeight + scrollTop + 500 > scrollHeight) {
          this.page++
          this.getContentList()
        }
      },
      // 获取文章列表
      getContentList () {
        const params = {
          page: this.page,
          limit: 6
        }
        getContentList(params).then(res => {
          const data = res.data
          if (data.status === 0) {
            if (data.result.length === 0) {
              this.isShowDialog = true
              setTimeout(() => {
                this.isShowDialog = false
              }, 2000)
              return
            }
            data.result.forEach(item => {
              item.update_time = item.update_time.split('T')[0]
            })
            this.contentList.push(...data.result)
            // console.log(this.contentList)
          }
        })
      },
      // 点击加载更多
      onMoreClick () {
        this.page++
        this.getContentList()
      }
    }
  }
</script>

<style scoped lang="scss">
</style>

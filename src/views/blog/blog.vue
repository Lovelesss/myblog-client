<template>
    <div class="blog-category">
        <Banner />
        <BlogContent :contentList="contentList" :category="category"/>
    </div>
</template>

<script>
  import Banner from '../../components/base/Banner'
  import BlogContent from '../../components/blog/BlogContent'
  import { getContentList, getCategory } from '../../api'
  import { throttle } from '../../utils/util'

  export default {
    name: 'blog',
    components: {
      Banner,
      BlogContent
    },
    data () {
      return {
        contentList: [],
        category: [],
        page: 1,
        isShowDialog: false,
        scroll: 0
      }
    },
    mounted () {
      this.$nextTick(() => {
        this.getCategory()
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
        if (clientHeight + scrollTop + 300 > scrollHeight) {
          this.page++
          this.getContentList()
        }
      },
      // 获取文章分类
      getCategory () {
        getCategory().then(res => {
          const data = res.data
          if (data.status === 0) {
            this.category = data.result
          }
        })
      },
      // 获取文章列表
      getContentList () {
        const params = {
          page: this.page,
          limit: 20
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
  .blog-category {
    min-height: calc(100vh - 200px);
    background: #efefef;
  }
</style>

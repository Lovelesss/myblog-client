<template>
  <div class="blog-content">
    <div class="blog-category">
      <button :class="{active: currentIndex === 100}" @click="onCategoryChange('全部', 100)" >全部</button>
      <button
        v-for="(category, index) in category"
        :key="index"
        :class="{active: index === currentIndex}"
        @click="onCategoryChange(category.category_name, index)"
      >
        {{category.category_name}}
      </button>
    </div>
    <div class="blog-wrapper">
      <div class="blog"
           v-for="(content, index) in articleList"
           :key="index"
      >
        <div class="blog-img" @click="onArticleClick(content.title)">
<!--          <img src="../../assets/images/680x440.png" alt="">-->
          <img :src="content.imgUrl" alt="">
        </div>
        <div class="blog-info" @click="onArticleClick(content.title)">
          <h3 class="blog-title">{{content.title}}</h3>
          <p class="blog-desc">
            {{content.article_desc}}
          </p>
        </div>
        <div class="blog-other">
          <div class="blog-tag">
            <span>javascript</span>
          </div>
          <div class="update-time">{{content.update_time}}</div>
        </div>
      </div>
      <i></i><i></i><i></i><i></i><i></i>
    </div>
  </div>
</template>

<script>
  import { getCategoryArticle } from '../../api'

  export default {
    name: 'BlogContent',
    props: {
      contentList: Array,
      category: Array
    },
    data () {
      return {
        currentIndex: 100,
        articleList: [],
        lastCategory: '全部' // 上一次请求分类名称，防止重复请求
      }
    },
    mounted () {
      this.$nextTick(() => {
        for (let i = 0; i < this.contentList.length; i++) {
          this.contentList[i].md_str = unescape(this.contentList[i].md_str)
          this.contentList[i].html_str = unescape(this.contentList[i].html_str)
        }
        this.articleList = this.contentList
      })
    },
    methods: {
      // 切换分类
      onCategoryChange (category, index) {
        if (category === this.lastCategory) {
          // console.log(this.lastCategory)
          return
        }
        this.currentIndex = index
        const params = {
          categoryName: category
        }
        getCategoryArticle(params).then(res => {
          const data = res.data
          if (data.status === 0) {
            this.articleList = data.result
            data.result.forEach(item => {
              item.update_time = item.update_time.split('T')[0]
            })
            this.lastCategory = category
          }
        })
      },
      onArticleClick (title) {
        this.$router.push(`/detail?title=${title}`)
      },
      onMoreClick () {
        this.$emit('onMoreClick')
      }
    }
  }
</script>

<style scoped lang="scss">
  .blog-content {
    .blog-category {
      background: #fff;
      padding: 0 50px 10px;
      /*height: 60px;*/
      display: flex;
      flex-flow: wrap;
      align-items: center;

      button {
        height: 32px;
        width: 100px;
        outline: none;
        border: 1px solid #eee;
        border-radius: 5px;
        background: #fff;
        font-size: 14px;
        color: #444;
        margin-right: 10px;
        margin-top: 10px;

        &:hover {
          border: 1px solid #999999;
          cursor: pointer;
        }
      }

      .active {
        background: #3399ff;
        color: #fff;
      }
    }

    .blog-wrapper {
      display: flex;
      justify-content: space-between;
      flex-flow: wrap;
      padding: 0 50px 100px;

      i {
        width: 19%;
      }
      /*&:after {*/
      /*  content: "";*/
      /*  flex: auto;*/
      /*}*/

      .blog {
        display: flex;
        flex-direction: column;
        &:hover {
          cursor: pointer;
          box-shadow: 5px 5px #eaeaea;
        }
        /*&:last-child {*/
        /*  margin-right: auto;*/
        /*}*/

        /*max-width: 330px;*/
        max-width: 19%;
        min-width: 280px;
        margin-top: 15px;
        background: #fff;

        .blog-img {
          width: 100%;
          max-height: 240px;
          overflow: hidden;

          img {
            width: 100%;
          }
        }

        .blog-info {
          flex: 1;
          padding: 20px;
          min-height: 100px;
          border-bottom: 1px solid #eee;

          h3 {
            text-align: center;
            color: #444;
          }

          .blog-desc {
            text-indent: 26px;
            margin-top: 10px;
            font-size: 13px;
            color: #555;
          }
        }

        .blog-other {
          display: flex;
          justify-content: space-between;
          align-items: center;
          padding: 0 20px;
          height: 32px;
          font-size: 13px;
          color: #555;

          .blog-tag:hover {
            text-decoration: underline;
            cursor: pointer;
          }
        }
      }
    }
  }
  @media (max-width: 1366px) {
    .blog-content {
      .blog-category {
        button {
        }
      }

      .blog-wrapper {
        &:after {
          content: "";
          min-width: 300px;
        }
        .blog {
          max-width: 24%;
          .blog-img {
          }

          .blog-info {
            h3 {
            }

            .blog-desc {
            }
          }

          .blog-other {
            .blog-tag:hover {
            }
          }
        }
      }
    }
  }
  @media (max-width: 1240px) {
    .blog-content {
      .blog-category {
        button {
        }
      }

      .blog-wrapper {
        &:after {
          content: "";
          min-width: 300px;;
        }
        .blog {
          max-width: 33%;
          .blog-img {
          }

          .blog-info {
            h3 {
            }

            .blog-desc {
            }
          }

          .blog-other {
            .blog-tag:hover {
            }
          }
        }
      }
    }
  }
  @media (max-width: 960px) {
    .blog-content {
      .blog-category {
        button {
        }
      }

      .blog-wrapper {
        &:after {
          content: "";
          min-width: 300px;;
        }
        .blog {
          max-width: 49%;
          .blog-img {
          }

          .blog-info {
            h3 {
            }

            .blog-desc {
            }
          }

          .blog-other {
            .blog-tag:hover {
            }
          }
        }
      }
    }
  }
  @media (max-width: 768px) {
    .blog-content {
      .blog-category {
        padding: 0 10px 10px;
        button {
        }
      }

      .blog-wrapper {
        display: flex;
        /*flex-direction: row;*/
        justify-content: center;
        padding: 0 10px 40px;
        .blog {
          max-width: 100%;
          .blog-img {
          }

          .blog-info {
            h3 {
            }

            .blog-desc {
            }
          }

          .blog-other {
            .blog-tag:hover {
            }
          }
        }
      }
    }
  }
</style>

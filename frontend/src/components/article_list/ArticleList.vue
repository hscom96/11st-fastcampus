<template>
  <div class="article-list">
    <div class="inner">
      <header>
        <h1>중고거래</h1>
        <button
          class="btn-write"
          @click="onClickWriteButton">
          <i class="fa-solid fa-pencil"></i>
          글쓰기
        </button>
      </header>

      <main>
        <ArticleItem
          v-for="article in articleList"
          :key="article.article_id"
          :article="article"
          class="article" />
      </main>

      <footer>
        <button
          class="more"
          @click="addArticleList">
          <Loading v-if="isLoading" />
          <span v-else>더보기</span>
        </button>
      </footer>
    </div>
  </div>
</template>

<script>
import requestArticleList from '~/utils/api/article_list'
import Loading from './loading.vue'
import ArticleItem from './ArticleItem'

export default {
  components: { 
    Loading,
    ArticleItem,
  },
  data() {
    return {
      isLoading: false,
      articleList: [],
      requestPage: 0,
      requestSize: 5,
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    init() {
      this.addArticleList()
    },
    async addArticleList() {
      if(this.isLoading) {
        return
      }

      try {
        this.isLoading = true
        
        const result = await requestArticleList(this.requestPage, this.requestSize)
                              .then(result => result.data.data)

        if(this.requestPage + 1 > result.total_page) {
          throw new Error('더 이상 페이지가 존재하지 않습니다')
        }
        
        this.articleList = [...this.articleList, ...result.content]
        this.requestPage++
      }
      catch(error) {
        alert(error)
      } finally {
        this.isLoading = false
      }
    },
    onClickWriteButton() {
      document.location.href = '/write'
    }
  }
}
</script>

<style lang="scss" scoped>
.article-list {
  background-color: #F7F8F9;
  min-height: calc(100vh - 190px);
  box-sizing: border-box;
  padding-top: 50px;
  padding-bottom: 50px;
  color: #495057;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;

  .inner {
    background-color: #FFFFFF;
    width: 750px;
    border-radius: 8px;
    box-shadow: 1px 1px 1px rgba(180, 180, 180, 0.25);
  
    header {
      width: 90%;
      margin: 20px auto;
      display: flex;
      justify-content: space-between;
      align-items: center;

      h1 {
        font-size: 18px;
        font-weight: 700;
      }

      .btn-write {
        background-color: $color-primary;
        color: #FFFFFF;
        border: none;
        border-radius: 4px;
        padding: 6px;
        cursor: pointer;
      }
    }

    main {
      .article {
        border-bottom: 1px solid $color-light;

        &:last-child {
          border: none;
        }
      }
    }

    footer {
      .more {
        background-color: #FFFFFF;
        border: none;
        width: 100%;
        height: 40px;
        border-top: 1px solid $color-light;
        border-radius: 0 0 8px 8px;
        cursor: pointer;
      }
    }
  }
}
</style>

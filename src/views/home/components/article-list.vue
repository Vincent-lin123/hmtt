<template>
    <div class="article-list">
        <van-pull-refresh v-model="isLoading" success-text="刷新成功" @refresh="onRefresh">
            <van-list v-model="loading" :error.sync="error" error-text="请求失败，点击重新加载" :finished="finished" finished-text="没有更多了" @load="onLoad">
                <article-item v-for="(article, index) in list" :key="index" :article="article"></article-item>
            </van-list>
        </van-pull-refresh>
    </div>
</template>
<script>
import { getArticles } from '../../../api/article'
import ArticleItem from '../../../components/article-item'
export default {
  name: 'ArticleList',
  components: {
    ArticleItem
  },
  props: {
    channel: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      list: [],
      loading: false,
      finished: false,
      timestamp: null,
      error: false,
      isLoading: false
    }
  },
  computed: {},
  watch: {},
  created () {},
  mounted () {},
  methods: {
    async onRefresh () {
      try {
        const { data } = await getArticles({
          channel_id: this.channel.id,
          timestamp: Date.now(),
          with_top: 1
        })
        const { results } = data.data
        this.list.unshift(...results)
        this.isLoading = false
      } catch (err) {
        this.isLoading = false
      }
    },
    async onLoad () {
      try {
        const { data } = await getArticles({
          channel_id: this.channel.id,
          timestamp: this.timestamp || Date.now(),
          with_top: 1
        })
        const { results } = data.data
        this.list.push(...results)
        this.loading = false
        if (results.length) {
          this.timestamp = data.data.pre_timestamp
        } else {
          this.finished = true
        }
      } catch (err) {
        this.error = true
        this.loading = false
      }
    }
  }
}
</script>

<style lang="less" scoped>
.article-list {
    height: 80vh;
    overflow-y: auto;
}
</style>

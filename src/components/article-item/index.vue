<template>
    <van-cell class="article-item"></van-cell>
</template>

<script>
export default {
  name: 'ArticleItemIndex',
  components: {},
  props: {
    article: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      active: 0
    }
  },
  computed: {},
  watch: {},
  created () {},
  mounted () {},
  methods: {
  }
}
</script>

<style lang="less" scoped>
</style>

<template lang="pug">
  div.side
    div.side-top
      FrontConfig(@configured="setConfigured" @change="fetch")
      Logo
      Search
    Topics(ref="Topics" :scroll-config="scrollConfig" :configured="configured")
</template>

<script>
  import TopicService from '@/entities/Topic/service'
  import ArticleMixin from '@/mixins/Article'
  import ConfigMixin from '@/mixins/Config'
  import FrontConfig from './Config'
  import Logo from './Logo'
  import Search from './Search'
  import Topics from './Topics'

  export default {
    name: 'Side',
    mixins: [ ConfigMixin, ArticleMixin, ],
    components: { FrontConfig, Logo, Search, Topics, },
    props: {
      scrollConfig: Object,
    },
    data () {
      return {
        configured: false,
      }
    },
    methods: {
      async setConfigured (_config) {
        this.configured = true

        this.$emit('configured')

        return await this.fetch(_config)
      },
      async fetch () {
        if (!this.configured) return

        if (!this.routedArticleId()) {
          const firstTopic = await TopicService.first()

          this.toArticle(firstTopic.id)
        }
        else {
          await this.$refs.Topics.fetch()
        }
      },
    },
  }
</script>

<style lang="scss">
  @import "~@/assets/scss/colors";
  @import "~@/assets/scss/scroll";

  .side {
    .side-top {
      display: flex;
      flex-direction: column;
      text-align: center;
      background-color: $sideTopBackgroundColor;
      padding-top: .5rem;
      color: $sideTextColor;

      #TopicSearch {
        margin-top: .5rem;
      }
    }
  }
</style>

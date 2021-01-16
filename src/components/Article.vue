<template>
  <div class="article">
    <!-- 如果正在加载显示此div -->
    <div class="loading" v-if="isLoading">
      <img src="../assets/loading.gif" alt="">
    </div>
    <div v-else>
      <div class="topic_header">
        <div class="topic_title">{{ post.title }}</div>
        <ul>
          <li>•发布于：{{ post.create_at |formatDate }}</li>
          <li>•作者：{{ post.author.loginname }}</li>
          <li>•{{ post.visit_count }}次浏览</li>
          <li>•来自{{ post|tabFormatter }}</li>
        </ul>
        <div v-html="post.content" class="topic_content"></div>
      </div>
      <div id="reply">
        <div class="topbar">回复</div>
        <div v-for="(reply,index) in post.replies" class="replySec">
          <div class="replyUp">
            <router-link :to="{
              name:'user_info',
              params:{
                name:reply.author.loginname
              }
              }">
              <img :src="reply.author.avatar_url" alt="">
            </router-link>
            <router-link :to="{
              name:'user_info',
              params:{
                name:reply.author.loginname
              }
              }">
              <span>{{ reply.author.loginname }}</span>
            </router-link>
            <span>
              {{ index + 1 }}楼
            </span>
            <span v-if="reply.ups.length>0 ">
              {{ reply.ups.length }}
            </span>
            <span v-else></span>
          </div>
          <p v-html="reply.content"></p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Article",
  data() {
    return {
      isLoading: false,//是否正在加载
      post: {}//代表当前文章页的所有内容，左右属性
    }
  },
  methods: {
    //   https://cnodejs.org/api/v1/topics
    getArticleData() {
      this.$http.get(`https://cnodejs.org/api/v1/topics/${this.$route.params.id}`)
        .then(res => {
          if (res.data.success === true) {
            this.isLoading = false;
            this.post = res.data.data;
          }
        })
        .catch(function (err) {
          console.log(err)
        })
    }
  },
  beforeMount() {
    this.isLoading = true;
    this.getArticleData();
  },
  watch: {
    '$route'(to, from) {
      this.getArticleData()
    }
  }
}
</script>
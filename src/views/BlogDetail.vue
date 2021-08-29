<template>
  <div>
    <Header></Header>

    <div class="mblog">
      <h2>{{ blog.title }}</h2>
      <template label="操作" v-if="ownBlog">
        <el-button
          type="success"
          icon="el-icon-check"
          @click="handleEdit(blog)"
          >编辑</el-button
        >
        <el-button
          type="danger"
          icon="el-icon-delete"
          @click="handleDelect(blog)"
          >删除</el-button
        >
      </template>
      <el-divider></el-divider>
      <div class="markdown-body" v-html="blog.content"></div>
    </div>
  </div>
</template>

<script>
import "github-markdown-css";
import Header from "../components/Header";

export default {
  name: "BlogDetail.vue",
  components: { Header },
  data() {
    return {
      blog: {
        id: "",
        title: "",
        content: "",
      },
      ownBlog: false,
    };
  },
  methods: {
    handleDelect(blog) {
      const _this = this;
      this.$axios
        .delete("/blog/" + blog.id, {
          headers: {
            Authorization: localStorage.getItem("token"),
          },
        })
        .then((res) => {
          console.log(res);
          _this.$alert("操作成功", "提示", {
            confirmButtonText: "确定",
            callback: (action) => {
              _this.$router.push("/blogs");
            },
          });
        });
    },
    handleEdit(blog) {
      this.$router.push({ name: "BlogEdit", params: { bolgId: blog.id } });
    },
  },
  created() {
    const blogId = this.$route.params.blogId;
    console.log(blogId);
    const _this = this;
    this.$axios.get("/blog/" + blogId).then((res) => {
      const blog = res.data.data;
      _this.blog.id = blog.id;
      _this.blog.title = blog.title;

      var MardownIt = require("markdown-it");
      var md = new MardownIt();

      var result = md.render(blog.content);
      _this.blog.content = result;
      _this.ownBlog = blog.userId === _this.$store.getters.getUser.id;
    });
  },
};
</script>

<style scoped>
.mblog {
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  width: 100%;
  min-height: 700px;
  padding: 20px 15px;
}
</style>
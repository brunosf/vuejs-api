<template>
  <main class="container">
    <article class="center">
      <section class="profile">
        <figure class="avatar">
          <img :src="avatar">
        </figure>
        <h1 class="nickname">{{ nickname }}</h1>
        <p class="name">{{ name }}</p>
      </section>

      <section class="repo-list">
        <a class="repo-item" v-for="repo in repos" :key="repo.id" :href="repo.html_url">
          <small class="repo-fullname">{{ repo.full_name }}</small>
          <h2 class="repo-name">{{ repo.name }}</h2>
        </a>
      </section>
    </article>
  </main>
</template>

<script>
import axios from "axios";

export default {
  name: "Githuber",
  props: {
    user: String
  },
  created() {
    const api = axios.create({
      baseURL: "https://api.github.com"
    });

    api
      .get(`/users/${this.$props.user}`)
      .then(response => {
        console.log(response.data);
        const {
          avatar_url,
          name,
          login,
          public_repos,
          repos_url
        } = response.data;

        this.avatar = avatar_url;
        this.name = name;
        this.nickname = login;

        if (public_repos) {
          axios.get(repos_url).then(response => {
            this.repos.push(...response.data);
          });
        }
      })
      .catch(error => error);
  },
  data() {
    return {
      avatar: "",
      name: "",
      nickname: "",
      repos: []
    };
  }
};
</script>

<style scoped>
.avatar {
  overflow: hidden;
  border-radius: 50%;
  width: 150px;
  height: 150px;
  text-align: center;
  margin: 0 auto;
}

.center {
  max-width: 750px;
  padding: 100px 30px;
  margin: 0 auto;
  border-radius: 5px;
  background-color: #36393f;
}

.nickname {
  font-size: 14px;
  font-weight: 400;
  color: hsla(0, 0%, 100%, 0.2);
  margin: 20px 0 0;
}

.name {
  font-size: 28px;
  font-weight: 600;
  color: #ffffff;
}

.repo-list {
  display: flex;
  flex-flow: row wrap;
  align-items: flex-start;
  justify-content: space-between;
  border-top: 1px solid hsla(0, 0%, 100%, 0.06);
  margin-top: 30px;
  padding-top: 30px;
}

.repo-item {
  flex: 0 1 30%;
  border-radius: 5px;
  padding: 10px;
  margin: 10px 0;
  background-color: rgba(114, 118, 125, 0.3);
  text-decoration: none;
}

.repo-fullname {
  font-size: 14px;
  text-align: left;
  display: block;
  color: #ccc;
}

.repo-name {
  display: block;
  text-align: left;
  font-size: 22px;
  font-weight: 600;
  color: #ffffff;
}
</style>


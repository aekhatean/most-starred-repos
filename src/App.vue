<template>
  <ul>
    <li v-for="repo in repos" :key="repo.id">
      <RepoCard :repoInfo="repo"/>
    </li>
  </ul>
</template>

<script>
import { Octokit } from '@octokit/core';
import RepoCard from './components/RepoCard';

export default {
  name: 'App',
  created() {
    this.addRepos();
  },
  data() {
    return {
      repos: [],
      page: 1,
    }
  },
  components: {
    RepoCard
  },
  methods: {
    /**
     * Fetch repos from Github sorted by number of stars.
     * 
     * @param {func} callback
     * @param {int} page
     */
    async getRepos( callback, page = 1 ) {
      const repos = [];
      const today = new Date().toISOString().slice( 0, 10 );
      const octokit = new Octokit();
      const res = await octokit.request( 'GET /search/repositories', {
        headers: {
          accept: 'application/vnd.github.v3+json'
        },
        q: `created: > ${ today }`,
        sort: 'stars',
        order: 'desc',
        per_page: 100,
        page: page
      } );

      repos.push( res.data.items );
      callback( repos );
    },

    /**
     * Callback func to get fetched data from Github by the async func getRepos().
     */
    addRepos() {
      this.getRepos( ( reposList ) => {
        this.repos = reposList[ 0 ];
      });
    }
  }
}
</script>

<style>
body {
  background: #F1F3F6;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #0e4379;
  margin-top: 60px;
}

ul {
  padding: 0;
}

li {
  list-style: none;
  cursor: pointer;
}

</style>
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
      pageNum: 0,
      perPage: 100,
      remainingReposNum: 0,
      numReserved: false,
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
          accept: 'application/vnd.github.v3+json',
        },
        q: `created: > ${ today }`,
        sort: 'stars',
        order: 'desc',
        per_page: this.perPage,
        page: page
      } );

      // Push the result into the repos array.
      repos.push( res.data.items );

      if( !this.numReserved ) {
        this.remainingReposNum = res.data.total_count;
      }

      // Load more properties if exists (Github API pagination).
      const remainderPg = this.remainingReposNum % this.perPage;
      if(  remainderPg > 0 && remainderPg !== this.remainingReposNum ) {
        this.remainingReposNum = this.remainingReposNum - this.perPage;
        const respons = await this.getRepos( null, this.paginate( this.remainingReposNum, this.perPage, page ) );
        repos.push( respons );
      }

      // Do not call the callback func while 
      if( typeof callback === 'function' ) {
        callback( repos );
        this.numReserved = true;
      }
    },

    /**
     * Callback func to get fetched data from Github by the async func getRepos().
     */
    addRepos() {
      this.getRepos( ( reposList ) => {
        this.repos = reposList[ 0 ];
      } );
    },

    /**
     * Go through Github API pagination if results exceed the number of results per page (maximum res per page is 100).
     * 
     * @param {int} remainingRepos
     * @param {int} perPage
     * @param {int} pageNum
     */
    paginate( remainingRepos, perPage, pageNum ) {
      if( remainingRepos % perPage > 0 ) {
        remainingRepos = remainingRepos - perPage;
        this.remainingReposNum = remainingRepos;
        return pageNum++;
      }
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
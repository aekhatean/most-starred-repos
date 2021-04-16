<template>
    <!-- Only show cards between today and 30 days ago -->
    <article class="grid-container" v-show="getDateDiff() < 30">
        <div class="avatar-container">
            <img :src="repoInfo.owner.avatar_url" alt="avatar">
        </div>
        <div class="info-container">
            <h4 class="repo-title"><a :href="repoInfo.html_url" target="_blank">{{ repoInfo.name }}</a></h4>
            <div class="repo-description">
                <!-- description text should not exceed 100 characters -->
                <p v-if="repoInfo.description.length < 100">{{ repoInfo.description }}</p>
                <p v-else>
                    {{ repoInfo.description.substring( 0, 100 ) }}
                    <a :href="repoInfo.html_url" target="_blank">..See more</a>
                </p>
            </div>
        </div>
        <div class="data-container">
            <small class="repo-stars">{{ repoInfo.stargazers_count }} stars</small>
            <small class="repo-issues">{{ repoInfo.open_issues }} issues</small>
            <small class="repo-owner">Created {{ getDateDiff() }} days ago by {{ repoInfo.owner.login }}</small>
        </div>
    </article>
</template>

<script>
/**
 * Create card components to show repo name, description, owner name, creation date,
 * and num of stars and issues.
 */
export default {
    name: 'RepoCard',
    props: [
        'repoInfo'
    ],
    methods: {
        /**
         * Get date diffrence between today and 30 days ago.
         */
        getDateDiff() {
            const today = new Date();
            const createdDay = new Date( this.repoInfo.created_at);
            const diffTime = Math.abs( today - createdDay );
            const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24)); 
            return diffDays;
        }
    }
}
</script>

<style scoped>
a {
    text-decoration: none;
}

.grid-container {
    display: grid;
    grid-template-columns: 20% 80%;
    grid-template-rows: 25% 25% 25% 25%;
    background-color: #F1F3F6;
    border-radius: 5px;
    max-width: 800px;
    max-height: 200px;
    margin: 20px auto 20px auto;
    box-shadow: 6px 6px 6px rgb(55 84 170 / 10%), -3px -3px 11px #ffffff9c;
    grid-template-areas:
    "avatar info"
    "avatar info"
    "avatar info"
    "avatar data";
    cursor:initial;
}

.avatar-container {
    grid-area: avatar;
    display: flex;
}


.avatar-container > img {
    margin: 9px;
    border-radius: 5px;
    max-height: 100px;
    max-width: 100px;
    align-self: center;
}

.info-container, .data-container {
    text-align: left;
    margin-left: 5px;
}

.info-container {
    grid-area: info;
    display: grid;
    grid-template-rows: 33.3% 33.3% 33.3%;
    grid-template-columns: auto;
    grid-template-areas: 
    "title"
    "desc"
    "desc";
}

.repo-title {
    grid-area: title;
    margin-bottom: 4em;
    max-width: 500px;
}

.repo-title > a {
  color: #0e4379;
}

.repo-description {
    grid-area: desc;
    max-width: 500px;
}


.data-container {
    grid-area: data;
    display: grid;
    grid-template-columns: 25% 25% 25% 25%;
    grid-template-rows: auto;
    grid-template-areas: "stars issues owner owner";
}

.repo-stars {
    grid-area: stars;
}

.repo-issues {
    grid-area: issues;
}

.repo-owner {
    grid-area: owner;
}

@media only screen and (max-width: 560px){
    .grid-container {
        display: grid;
        grid-template-rows: 25% 25% 25% 25%;
        grid-template-columns: 33.3% 33.3% 33.3%;
        grid-template-areas:
        "avatar info info"
        "avatar info info"
        "avatar info info"
        "data data data";
        max-height: 300px;
    }

    .avatar-container {
        grid-area: avatar;
        align-self: center;
    }

    .info-container {
        grid-area: info;
        display: grid;
        grid-template-rows: 33.3% 33.3% 33.3%;
        grid-template-areas: 
        "title"
        "desc"
        "desc";
    }
    .repo-title {
        grid-area: title;
        max-width: 245px;
    }
    .repo-description {
        grid-area: desc;
        max-width: 200px;
        word-wrap: break-word;
    }

    .data-container {
        grid-area: data;
        display: grid;
        grid-template-columns: 25% 25% 25% 25%;
        grid-template-areas: " stars issues owner owner";
    }

    .repo-stars {
        grid-area: stars;
    }
    .repo-issues {
        grid-area: issues;
    }
    .repo-owner {
        grid-area: owner;
    }
}
</style>
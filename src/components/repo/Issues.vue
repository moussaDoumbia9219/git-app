<template>
  <div v-if="issues.length > 0">
    <button @click="showIssues = !showIssues">
      {{ showIssues ? "Hide" : "Show" }} issues
    </button>
    <div v-if="showIssues">
      <div v-for="i of issues" :key="i.id">
        <h3>{{ i.title }}</h3>

        <a :href="i.url">Go to issue</a>

        <IssueComments :owner="owner" :repo="repo" :issueNumber="i.number" />
      </div>
    </div>
  </div>
</template>

<script>
import { octokitMixin } from "../../mixins/octokitMixin";
import IssueComments from "./issue/Comments.vue";

export default {
  name: "RepoIssues",
  components: {
    IssueComments,
  },
  data() {
    return {
      issues: [],
      showIssues: false,
    };
  },
  props: {
    owner: {
      type: String,

      required: true,
    },

    repo: {
      type: String,

      required: true,
    },
  },
  mixins: [octokitMixin],
  methods: {
    async getRepoIssues(owner, repo) {
      const octokit = this.createOctokitClient();
      const { data: issues } = await octokit.issues.listForRepo({
        owner,
        repo,
      });
      this.issues = issues;
    },
  },
  watch: {
    owner: {
      handler(val) {
        this.getRepoIssues(val, this.repo);
      },
    },

    repo: {
      handler(val) {
        this.getRepoIssues(this.owner, val);
      },
    },
  },

  created() {
    this.getRepoIssues(this.owner, this.repo);
  },
};
</script>

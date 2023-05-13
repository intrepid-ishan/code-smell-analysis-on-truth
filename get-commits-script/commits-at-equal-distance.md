```js
// Below code extracts 10 commits from all commits at equal distance of any given repo
// For below script this repo is used: https://github.com/google/truth

// Define the GitHub API endpoint and repo info
const endpoint = 'https://api.github.com/repos/';
const owner = 'google';
const repo = 'truth';
const accessToken = 'token'; // TODO: replace with your personal access token

// Define the number of commits you want to extract
const numCommits = 10;

// Define the page size for each API request
const pageSize = 100;

// Define a function to retrieve commits from a given page
async function getCommits(page) {
  const response = await fetch(
    `${endpoint}${owner}/${repo}/commits?per_page=${pageSize}&page=${page}`,
    {
      headers: {
        Authorization: `token ${accessToken}`,
      },
    }
  );
  const commits = await response.json();
  return commits;
}

// Fetch the repo's commit history using the GitHub API
async function fetchCommits() {
  let allCommits = [];
  let page = 1;
  while (true) {
    const commits = await getCommits(page++);
    if (commits.length === 0) {
      break;
    }
    allCommits = allCommits.concat(commits);
  }
  return allCommits;
}

// Extract the specified number of commits at equal distance
fetchCommits()
  .then((commits) => {
    const commitDistance = Math.floor(commits.length / numCommits);
    let selectedCommits = [];
    for (let i = 0; i < numCommits; i++) {
      const commit = commits[i * commitDistance];
      selectedCommits.push({
        sha: commit.sha,
        time: commit.commit.author.date,
      });
    }
    console.log(selectedCommits);
  })
  .catch((error) => console.error(error));
```

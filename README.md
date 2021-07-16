- 👋 Hi, I’m @KeKo6988
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
KeKo6988/KeKo6988 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
{
  "language": "shell",
  "os": [
    "linux"
  ],
  "dist": "bionic",
  "branches": {
    "only": [
      "master",
      "/^v\\d+\\.\\d+/"
    ]
  },
  "notifications": {
    "email": [
      {
        "enabled": false
      }
    ],
    "slack": [
]

},
"jobs": {
"include": [
{
"name": "Export Github Repositories",
"if": "type IN (push, cron) AND branch = master",
"language": "python",
"git": {
"depth": false
},
"script": [
".travis/export-github-repo.sh web3.js/ solana-web3.js",
".travis/export-github-repo.sh explorer/ explorer"
]
},
{
"if": "type IN (api, cron) OR tag IS present",
"name": "macOS release artifacts",
"os": "osx",
"language": "rust",
"rust": "stable",
"install": [
"source ci/rust-version.sh",
"PATH="/usr/local/opt/coreutils/libexec/gnubin:$PATH"",
"readlink -f ."
],
"script": [
"source ci/env.sh",
"ci/publish-tarball.sh"
],
"deploy": [
{
"provider": "s3",
"access_key_id": "$AWS_ACCESS_KEY_ID",
"secret_access_key": "$AWS_SECRET_ACCESS_KEY",
"bucket": "release.solana.com",
"region": "us-west-1",
"skip_cleanup": true,
"acl": "public_read",
"local_dir": "travis-s3-upload",
"on": {
"all_branches": true
}
},
{
"provider": "releases",
"token": "$GITHUB_TOKEN",
"skip_cleanup": true,
"file_glob": true,
"file": [
"travis-release-upload/"
],
"on": {
"tags": true
}
}
]
},
{
"if": "type IN (api, cron) OR tag IS present",
"name": "Windows release artifacts",
"os": "windows",
"language": "rust",
"rust": "stable",
"install": [
"source ci/rust-version.sh",
"PATH="/usr/local/opt/coreutils/libexec/gnubin:$PATH"",
"readlink -f ."
],
"script": [
"source ci/env.sh",
"ci/publish-tarball.sh"
],
"deploy": [
{
"provider": "s3",
"access_key_id": "$AWS_ACCESS_KEY_ID",
"secret_access_key": "$AWS_SECRET_ACCESS_KEY",
"bucket": "release.solana.com",
"region": "us-west-1",
"skip_cleanup": true,
"acl": "public_read",
"local_dir": "travis-s3-upload",
"on": {
"all_branches": true
}
},
{
"provider": "releases",
"token": "$GITHUB_TOKEN",
"skip_cleanup": true,
"file_glob": true,
"file": [
"travis-release-upload/"
],
"on": {
"tags": true
}
}
]
},
{
"name": "explorer",
"if": "type = pull_request AND branch = master",
"language": "node_js",
"node_js": "lts/",
"cache": {
"directories": [
"~/.npm"
]
},
"before_install": [
".travis/affects.sh explorer/ .travis || travis_terminate 0",
"cd explorer"
],
"script": [
"npm run build",
"npm run format"
]
},
{
"name": "web3.js",
"if": "type = pull_request AND branch = master",
"language": "node_js",
"node_js": "lts/",
"services": [
"docker"
],
"cache": {
"directories": [
"/.npm"
]
},
"before_install": [
".travis/affects.sh web3.js/ .travis || travis_terminate 0",
"cd web3.js/",
"source .travis/before_install.sh"
],
"script": [
"../.travis/commitlint.sh",
"source .travis/script.sh"
]
},
{
"name": "docs",
"if": "type IN (push, pull_request) OR tag IS present",
"language": "node_js",
"node_js": "node",
"services": [
"docker"
],
"cache": {
"directories": [
"/.npm"
]
},
"before_install": [
"source ci/env.sh",
".travis/channel_restriction.sh edge beta || travis_terminate 0",
".travis/affects.sh docs/ .travis || travis_terminate 0",
"cd docs/",
"source .travis/before_install.sh"
],
"script": [
"source .travis/script.sh"
]
}
]
}
}

### To make that working in main repository (harp.gl) follow those steps:

1. Create github-bot account or use existing one.
    - in next steps you will need an email of created "bot user" and you will have to generate
    ssh key pair for that user using an email.
    - or you can use existing bot account (you just have to add SSH public key for it later)
    
2. Create new repository for storing reference images. In my case repository was called
"reference-images" with "images" folder in it where the reference images were stored.
    - add "bot user" as a contributor for that repository
    - update config file @here/harp-test-utils/config.json:
    
```json
{
    "referenceRepo": "git@github.com:arekspiewak1991/reference-images.git", // url for created repository
    "referenceImagesPath": "reference-images/images",                       // {repositoryName}/{images_directory_name}
    "referenceMainDir": "reference-images",                                 // {repositoryName}
    "referenceOutputPath": "./test/rendering",                              // will stay as is
    "referenceDataBranch": "master",                                        // repository branch where to push images
    "testResultsPath": "rendering-test-results",                            // will stay as is
    "baseReferenceImagePath": "https://raw.githubusercontent.com/arekspiewak1991/reference-images/master/images", // the URL,
    // for githubImageResolver (file: TestPathConfig.ts)
    // https://raw.githubusercontent.com/{username/company}/{referenceMainDir}/{referenceDataBranch}/{images_directory_name}
}
```

3. Setup SSH connection and add SSH key to TRAVIS CI. 
#####Run commands on your local machine
- `gem install travis` (install travis-ci if you have not installed before)
- `travis login` (and login with you github credentials - can use yours, not bot-user)
- `ssh-keygen -t rsa -b 4096 -C "bot-user@emai.com" -f git_deploy_key` - generate SSH key pair using bot-user email
- `travis encrypt-file git_deploy_key -r {user/repo_name} --add` - encrypt ssh private key and add it to TRAVIS (after running that command
you should check if new environment variables appeared in TRAVIS CI - no worries if you see some error related with "travis.yml not found" - ignore it)
 (helpful link: https://semantic-release.gitbook.io/semantic-release/recipes/git-hosted-services/git-auth-ssh-keys)
- copy encrypted file to main project folder (harp.gl/git_deploy_key.enc)
- copy `openssl aes-256-cbc... ` command to `.travis.yml` file (change existing one with yours `openssl aes-256-cbc -K $encrypted_498d4c4e8b8a_key -iv $encrypted_498d4c4e8b8a_iv -in git_deploy_key.enc -out /tmp/git_deploy_key -d`)
- run `git add git_deploy_key.enc .travis.yml` and push changes to repo 
- add public SSH key for github-bot

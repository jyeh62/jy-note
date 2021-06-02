---
type: Hook Pull Request
keywords: git, pull request
created : 2021/06/02/18:07
---

# Hook Pull Request

## GIT Web hook

git 관련 event를 지정된 서버로 전달
GitHub : /hook
Bitbucket :
    - URL : /bitbucket-hook
    - body : <https://confluence.atlassian.com/bitbucketserver0510/event-payload-951390742.html?utm_campaign=in-app-help&utm_medium=in-app-help&utm_source=stash#Eventpayload-pullrequest>

## Pull Request Branch

``` bash
    # GitHub 용
    # pull request checkout
    pr() {
        git fetch -fu origin refs/pull/$1/head:pr/$1 
        git checkout pr/$1
    }
    # branch 삭제                                                                                                        
    prc() {                                                                                                         
        git for-each-ref refs/heads/pr/* --format='%(refname)' | \
            while read ref; do git branch -D ${ref#refs/heads/}; done
    }
```

``` bash
# Bitbucket
pr() {
    git fetch -fu origin refs/pull/$1/head:pr/$1
    git checkout pr/$1
}                                                                                                           
```
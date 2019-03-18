---
layout: single
categories:
  - Blogging
title: Minimal Mistakes 를 적용한 Github Page 만들기
author: Gold
author-email: pnurep@gmail.com
description: Minimal Mistakes 를 적용한 Github Page 만들기에 대하여 알아봅니다.
tags:
  - Jekyll
  - Github Pages
last_modified_at: 2019-03-07T13:00:00+09:00
toc: true
publish: true
classes: wide
comments: true
sitemap :
  changefreq : daily
  priority : 1.0
---

<br>

# Minimal Mistakes 를 적용한 Github Page 만들기


## 적용하기

[https://github.com/mmistakes/minimal-mistakes](https://github.com/mmistakes/minimal-mistakes)

처음에 내가 사용한 방법은 Github Pages 의 기능중 하나인 jekyll remote theme 기능을 사용해 빠르게 블로그를 만드는 것이었다. 하지만 이 방법으로는 추후에 커스터마이징을 하고 싶다던가 할때는 remote theme 으로는 불가능하고 추가적인 폴더들과 로컬에서 jekyll 을 설치해 빌드해야 한다. 그렇기에 가장 내가 가장 추천하는 방법은 처음부터 적용하고 싶은 테마의 repo 를 fork 해서 그 repo 의 이름을 username.github.io 로 변경하는 것이다.


### Remove the Unnecessary

로컬에서 아래에 해당하는 폴더 및 파일들을 삭제해준다.

* .editorconfig
* .gitattributes
* .github
* /docs
* /test
* CHANGELOG.md
* minimal-mistakes-jekyll.gemspec
* README.md
* screenshot-layouts.png
* screenshot.png


### 필요한 폴더 생성

만약 로컬의 최상위에 아래의 폴더들이 없다면 생성해 주도록 하자.

* _posts : 자신이 작성한 포스팅들을 담는 폴더
* _pages : 사용할 페이지들을 담는 폴더 ex) 404.md, 샘플참조
* _drafts : 포스팅들의 드래프트를 담는 공간


### .gitignore 설정

먼저 로컬에서 shift + command + . 을 눌러 숨김파일까지 보이게 한 후 최상위 경로에 .gitignore 파일이 존재하는지를 확인한다.
없다면 생성하고 아래의 내용을 추가한다.

```
### Jekyll ###
_site
.sass-cache
.jekyll-cache/
.jekyll-metadata
Gemfile.lock
```

### Gemfile 수정

```
source "https://rubygems.org"

gem "jekyll", "~> 3.5"
gem "minimal-mistakes-jekyll"
```



## 설치

### Homebrew 설치

아래의 스크립트를 터미널에 붙여넣기 하여 Homebrew 를 설치한다.

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### Ruby 설치

jekyll 을 사용하려면 Ruby 를 설치해야한다.

```
brew update
brew upgrade
brew install rbenv
brew install ruby-build
rbenv init
rbenv install 2.5.0
rbenv rehash
rbenv global 2.5.0
```

rbenv init 커맨드를 실행해보면 자신의 shell 에 ``` eval “$(rbenv init -)” ``` 를 추가하라고 한다. 나는 zsh shell 을 사용하고 있기에 ~/.zshrc 에 추가해주었다. 이후 터미널을 껏다가 재시작(로그인) 하고 ruby -version 을 입력하면 루비버전이 2.5.0 으로 나올것이다.


나의 경우 기존에 2.3.0 버전이 설치되어 있었는데 2.5.0 버전을 설치하고 버전을 바꾸어 주어도 적용이 되지 않았다. 여러가지를 시도해보다 해결했다.

* .bash_profile 에도 ```eval "$(rbenv init -)"``` 을 추가해주고 터미널 재시작 후 source 로 새로고침을 해주는 방법.

그리고 설치중에 Permission 관련 에러가 발생할 수 있는데 그것에 대한 설명은 [Jekyll로 블로그 시작하기 위한 준비](https://blog.jungbin.kim/jekyll/2016/11/28/start-to-jekyll.html) 에 잘 설명되어 있으므로 참고하면 좋겠다.


### Jekyll 설치

```
$ gem install jekyll
```

### bundler 설치
jekyll 에서 사용되는 gem 을 관리하기 위해 bundler 를 설치한다.

```
$ gem install bundler
```

### 라이브러리 의존성 다운로드

로컬위치로 이동해 의존성들을 다운로드 해준다.

```
$ bundle install
```



## 실행

```
bundle exec jekyll serve

// 종료하려면 control + c
```

위의 커맨드는 jekyll 을 로컬에서 실행한다. http://localhost:4000/ 로 접속하면 현재 블로그의 로컬상태를 확인할 수 있다.


## 결론

위의 일련의 과정으로 기본적인 블로그를 사용하기 위한 세팅은 끝났다. 자세한 설정(_config.yml 수정 등...) 은 다른 여러 글에서 소개하고 있으니 참고하면 좋겠다.


<br><br>


{% if page.comments %}
<div id="post-disqus" class="container">
{% include disqus.html %}
</div>
{% endif %}



























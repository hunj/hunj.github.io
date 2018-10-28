---
layout: post
date: 2018-04-07
title:  asdf 를 이용한 버전 관리
subtitle: 통합 버전 관리자를 이용하여 rbenv, RVM, pyenv 등의 개발환경 설정을 더 편리하게 하자.
---

# 소개

웹 개발을 하다보면, rbenv, npm 등 각 프로그래밍 언어별 버전 관리자를 사용하게 됩니다.

하지만 여러 버전을 사용하게 되면 관리하기가 난잡해지기 마련입니다. [asdf](https://github.com/asdf-vm/asdf) 을 사용하면 각종 버전 관리자를 손쉽게 이용할 수 있습니다.

## 사용 예제

다음 예제는 처음 asdf 를 설치한 후, 루비 2.5.0 버전을 사용하게끔 설정하는 과정을 담았습니다:

```shell
$ asdf plugin-add ruby      # 루비 버전 관리자를 asdf에 등록
$ asdf install ruby 2.5.0   # asdf 를 통해 루비 2.5.0 버전을 설치
$ asdf global ruby 2.5.0    # 전역에서 루비 2.5.0 버전을 사용하게끔 설정
$ asdf reshim ruby 2.5.0    # 루비 2.5.0 버전을 reshim 하기
```

# 설치하기

- `brew` 를 사용하여 디펜던시를 설치합니다:

```shell
$ brew install coreutils automake autoconf openssl libyaml readline libxslt libtool unixodbc
```

- `git clone` 을 이용하여 홈 디렉토리에 설치합니다:

```shell
$ git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.6.0
```

- `bash` 에 경로를 등록합니다:
  - macOS bash 의 경우:
  ```shell
  $ echo -e '\n. $HOME/.asdf/asdf.sh' >> ~/.bash_profile
  $ echo -e '\n. $HOME/.asdf/completions/asdf.bash' >> ~/.bash_profile
  ```
  - [zsh](http://www.zsh.org/) ([oh-my-zsh](https://ohmyz.sh/) 포함) 의 경우:
  ```shell
  $ echo -e '\n. $HOME/.asdf/asdf.sh' >> ~/.zshrc
  $ echo -e '\n. $HOME/.asdf/completions/asdf.bash' >> ~/.zshrc
  ```

## 사용하기

위의 "사용 예제"를 참고하면 기본적인 플로우를 알 수 있습니다.

사용 가능한 플러그인(언어별 버전 관리자)들은 [여기 (asdf-plugins)](https://github.com/asdf-vm/asdf-plugins#plugin-list) 에서 확인할 수 있습니다.

#### 플러그인 추가

```shell
$ asdf plugin-add <name>
# 예제: asdf plugin-add erlang
```

#### 플러그인 목록 보기

```shell
$ asdf plugin-list
# asdf plugin-list
# java
# nodejs
```

```shell
$ asdf plugin-list --urls
# asdf plugin-list
# java            https://github.com/skotchpine/asdf-java.git
# nodejs          https://github.com/asdf-vm/asdf-nodejs.git
```

#### 플러그인 버전 관리

```shell
$ asdf install <name> <version>
# asdf install erlang 17.3

$ asdf current
# asdf current
# erlang 17.3 (set by /Users/hijinx/.tool-versions)
# nodejs 6.11.5 (set by /Users/hijinx/cool-node-project/.tool-versions)

$ asdf current <name>
# asdf current erlang
# 17.3 (set by /Users/hijinx/.tool-versions)

$ asdf uninstall <name> <version>
# asdf uninstall erlang 17.3
```

#### 플러그인 삭제

```shell
$ asdf plugin-remove <name>
# 예제: asdf plugin-remove erlang
```

### 전역 설정 (Global Settings)

전역 설정은 `$HOME/.tool-versions` 파일에 저장됩니다.

다음은 `.tool-versions` 파일의 모양새입니다:

```
ruby 2.2.0
nodejs 0.12.3
```


# 마치며

더 자세한 사항은 [asdf-vm](https://github.com/asdf-vm/asdf) 리포지토리에서 확인할 수 있습니다.

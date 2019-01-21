---
title: CircleCI集成Github

---

# 前言
随着前端技术的不断发展，项目复杂度的逐渐提高，前端已经逐渐由以往的界面抽象转变为组件抽象。与此同时，一些软件工程化的思想也开始慢慢在前端普及，编译、构建、单元测试、持续集成、持续部署等。这里，我们主要谈一下如何使用`CircleCI`来集成我们的前端项目，构建一整套的CI/CD流程。

# CircleCI
## CircleCI 连接 Github

`CircleCI`目前有两个版本，我们这里使用的是v2.
首先，打开[`CircleCI`官网](https://circleci.com/product/)，找到Product选项。

点击 Start Building for Free，这里会跳转到登录界面，`CircleCI`提供了两种不同的登录方式，分别为Github和Bitbucket。我们这里选择Github，然后允许读取权限。

进入`CircleCI`平台之后，选择Add Projects,这里会把你Github项目下所有的项目都列出来。选择需要集成的项目，然后在Operating System里面选择 Linux，Language选择 Node。
这里，我们已经完成了CircleCI集成Github的大部分工作。

## 配置CircleCI

### 创建配置文件
 - 在项目根目录下创建`.circleci`文件夹，然后增加`config.yml`文件。

### 编写config.yml

```  yml
# Javascript Node CircleCI 2.0 configuration file
#
# Check https://circleci.com/docs/2.0/language-javascript/ for more details
#
version: 2
jobs:
  build:
    branches:
      ignore:
        - gh-pages
    docker:
      # specify the version you desire here
      - image: circleci/node:latest
      
      # Specify service dependencies here if necessary
      # CircleCI maintains a library of pre-built images
      # documented at https://circleci.com/docs/2.0/circleci-images/
      # - image: circleci/mongo:3.4.4

    working_directory: ~/repo

    environment:
      - SOURCE_BRANCH: master
      - TARGET_BRANCH: gh-pages

    steps:
      - checkout

      - setup_remote_docker

      # Download and cache dependencies
      - restore_cache:
          keys:
          - v1-dependencies-{{ checksum "package.json" }}
          # fallback to using the latest cache if no exact match is found
          - v1-dependencies-

      - run: 
          name: Install dependencies
          command: npm install

      - save_cache:
          paths:
            - node_modules
          key: v1-dependencies-{{ checksum "package.json" }}
        
      # run tests!
      # - run: yarn test

      # run build!
      - deploy : 
          name: Run deploy
          command: ./deploy-ghpages.sh build
```

### 提交并推送改动到Github

### CircleCI自动构建
 `CircleCI`会自动监听我们项目的变化，并按照我们的配置的脚本自动开始构建!


下一章，我们会具体讲一下`config.yml`的配置以及编写
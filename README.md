### 基于GitFlow+Rebase+CommitLint+Standard-Version的开发流程规范

1. [GitFlow](https://www.cnblogs.com/cnblogsfans/p/5075073.html) ：简单来说
    - main(即原master)分支为受保护分支，开发者不能直接合并代码进去
    - 每次的开发(比如：功能开发，线上缺陷修复等)都必须从main切出新分支
    - 根据开发目的的不同切到不同的分支
        - 功能迭代 feature/<分支名>
        - 缺陷修复 bugfix/<分支名>
        - 预发布 release/<分支名>
    - 每次发布测试只能从`功能分支feature/<分支名>`/`缺陷修复分支bugfix/<分支名>`合并到`预发布分支release/<分支名>`
    - 发布上线只能从`预发布分支release/<分支名>`合并到main分支
2. [CommitLint](https://github.com/conventional-changelog/commitlint) ：校验commit message
    - 在`git commit`时添加符合`type(scope?): subject`格式的信息
    - | type | description |
      | :---- | :---- |
      | feat | 新增一个功能 |
      | fix | 修复一个Bug |
      | docs | 文档变更 |
      | style | 代码格式（不影响功能，例如空格、分号等格式修正） |
      | refactor | 代码重构 |
      | perf | 改善性能 |
      | test | 测试 |
      | build | 变更项目构建或外部依赖（例如scopes: webpack、gulp、npm等） |
      | ci | 更改持续集成软件的配置文件和package中的scripts命令，例如scopes: Travis, Circle等 |
      | chore | 变更构建流程或辅助工具 |
      | revert | 代码回退 |
    - 使用
    ```
    # 安装 husky ，使用 commit-msg 钩子
    npm install husky --save-dev
   
    # 安装 commitlint cli 和 conventional config
    npm install --save-dev @commitlint/config-conventional @commitlint/cli
    
    # 添加 commitlint 的配置文件
    echo "module.exports = {extends: ['@commitlint/config-conventional']}" > commitlint.config.js
   
    # 在 .huskyrc 或者 package.json 中添加钩子
    {
     "husky": {
       "hooks": {
         "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
       }
     }
    }
    ```
3. [Standard-Version] ：生成CHANGELOG，管理项目版本
    - 
4. [Rebase](http://jartto.wang/2018/12/11/git-rebase/)
    - rebase和merge各有优势合理使用
    - 只要你的分支上需要rebase的所有commits历史还没有被push过，就可以安全地使用rebase来操作。
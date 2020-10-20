### 基于gitflow+commitizen+commitlint+standard-version的规范化开发流程

1. git flow
    - [什么是git flow](https://danielkummer.github.io/git-flow-cheatsheet/index.zh_CN.html)
    - [如何使用git flow](https://www.cnblogs.com/cnblogsfans/p/5075073.html)
    - main(即原master)分支为受保护分支，开发者只能从其他分支合并，不能在这个分支直接修改，所有commit都要有tag
    - 每次的功能开发/线上缺陷修复，都必须从main切出新分支
    - 根据开发目的的不同切到不同的分支
        - 功能迭代 feature/<分支名>：基于develop分支创建，用于新功能开发
        - 缺陷修复 bugfix/<分支名>：基于main分支创建，bugfix
        - 预发布 release/<分支名>：基于develop分支创建，在此分支上测试
    - 每次发布测试只能从功能分支/缺陷修复分支合并到develop，然后从develop合并到release分支
    - 当release完成之后，将release的代码合并到main和develop分支
    - 使用命令`git flow <subcommand>`，或者使用工具(推荐sourcetree)
2. [commitizen](https://github.com/commitizen/cz-cli) ：界面化的commit message
    - 安装使用
    ```
    # 安装commitizen
    npm install commitizen -D
   
    # 初始化项目以使用cz-conventional-changelog
    npx commitizen init cz-conventional-changelog --save-dev --save-exact
   
    # 在 package.json 中添加命令
    "scripts": {
       "commit": "git add . && cz"
    }
    ```
    - 每次提交时执行`npm run commit`，选择/输入相应信息就行了
3. [commitlint](https://github.com/conventional-changelog/commitlint) ：校验commit message
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
   - 在每次commit时都会都commit message进行校验，不通过无法commit
   - **不推荐**如果要跳过校验可以添加`--no-verify`，`git commit -m 'msg' --no-verify`
4. [standard-version](https://github.com/conventional-changelog/standard-version) ：生成CHANGELOG，跟新项目版本号，并打tag
    - 安装使用
    ```
    # 安装standard-version
    npm install standard-version --save-dev
   
    # 在 package.json 中添加命令
    "scripts": {
        "release": "standard-version"
    }
   
    # 创建生成的changelog规则文件 .versionrc
    {
     "types": [
       {"type":"feat","section":"Features"},
       {"type":"fix","section":"Bug Fixes"},
       {"type":"test","section":"Tests", "hidden": true},
       {"type":"build","section":"Build System", "hidden": true},
       {"type":"ci","hidden":true}
     ]
    }
   
    # 可以在 package.json 中添加配置来不打tag，不生成changelog
    {
      "standard-version": {
        "skip": {
          "changelog": true,
          "tag": false
        }
      }
    }
    ```
    - 在每次发版之前通过`npm run release`命令来生成当次发布的内容的changelog文档，并更新版本号，打上tag号符合git flow的main分支必须有tag的规范
5. [rebase](http://jartto.wang/2018/12/11/git-rebase/)
    - 正常来说一个功能分支只能有一个 feat 的commit，一个bugfix分支只能有一个 fix 的commit
    - 如果有多个就需要通过`git rebase`来合并 commit
    - rebase和merge各有优势合理使用
    - 只要你的分支上需要rebase的所有commits历史还没有被push过，就可以安全地使用rebase来操作。
    - 通过`git rebase -i`命令来合并commit
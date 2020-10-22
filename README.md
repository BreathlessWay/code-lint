# 规范化开发流程

> 基于 git flow+commitizen+husky+commitlint+standard-version+git rebase 的分支及发布管理

1. 基于 git flow，适应实际开发的工作流
    - [什么是 git flow](https://danielkummer.github.io/git-flow-cheatsheet/index.zh_CN.html)
    - [如何使用 git flow](https://www.cnblogs.com/cnblogsfans/p/5075073.html)
    - main(即原 master)分支为受保护分支，开发者只能从其他分支合并，不能在这个分支直接修改，所有 commit 都要有 tag，开发者只能提交 merge request
    - 每次的功能开发/线上缺陷修复，都必须从 main 切出新分支
    - 根据开发目的的不同切到不同的分支
        - 功能迭代 feature/<分支名>：基于 main 分支创建，用于新功能开发
        - 缺陷修复 bugfix/<分支名>：基于 main 分支创建，修复线上 bug
        - 预发布 release/<分支名>：基于 main 分支创建，所有开发测试完毕需要发布的 feature 都先合并到此分支
    - 每次发布测试只能从功能分支/缺陷修复分支合并到相应的测试环境分支，比如 develop，qa 等
    - 功能的 bug 在对应的 feature 分支修改，再合并到相应测试分支
    - 当 release 完成之后，将 release 的代码合并到 main 分支
    - 使用命令`git flow <subcommand>`，或者使用工具(推荐 sourcetree)
2. [commitizen](https://github.com/commitizen/cz-cli) ：界面化的 commit message

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

3. [husky](https://github.com/typicode/husky)

    - 安装使用

    ```
    # 安装husky
    npm install husky --save-dev

    # 添加配置文件 .huskyrc
    {
        "hooks": {
            "pre-commit": "lint-staged",
            "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
        }
    }
    ```

    - 可以在 commit 之前对 commit message 校验，对代码进行校验等

4. [commitlint](https://github.com/conventional-changelog/commitlint) ：校验 commit message

    - 在`git commit`时添加符合`type(scope?): subject`格式的信息
    - | type     | description                                                                            |
      | :------- | :------------------------------------------------------------------------------------- |
      | feat     | 新增一个功能                                                                           |
      | fix      | 修复一个 Bug                                                                           |
      | docs     | 文档变更                                                                               |
      | style    | 代码格式（不影响功能，例如空格、分号等格式修正）                                       |
      | refactor | 代码重构                                                                               |
      | perf     | 改善性能                                                                               |
      | test     | 测试                                                                                   |
      | build    | 变更项目构建或外部依赖（例如 scopes: webpack、gulp、npm 等）                           |
      | ci       | 更改持续集成软件的配置文件和 package 中的 scripts 命令，例如 scopes: Travis, Circle 等 |
      | chore    | 变更构建流程或辅助工具                                                                 |
      | revert   | 代码回退                                                                               |
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

    - 在每次 commit 时都会都 commit message 进行校验，不通过无法 commit
    - **不推荐**如果要跳过校验可以添加`--no-verify`，`git commit -m 'msg' --no-verify`

5. [standard-version](https://github.com/conventional-changelog/standard-version) ：生成 CHANGELOG，跟新项目版本号，并打 tag

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

    - 在每次发版之前通过`npm run release`命令来生成当次发布的内容的 changelog 文档，并更新版本号，打上 tag 号符合 git flow 的 main 分支必须有 tag 的规范

6. [rebase](http://jartto.wang/2018/12/11/git-rebase/)
    - 正常来说一个功能分支只能有一个 feat 的 commit，一个 bugfix 分支只能有一个 fix 的 commit
    - 如果有多个就需要通过`git rebase`来合并 commit
    - rebase 和 merge 各有优势合理使用
    - 只要你的分支上需要 rebase 的所有 commits 历史还没有被 push 过，就可以安全地使用 rebase 来操作。
    - 通过`git rebase -i`命令来合并 commit

> 示例

1. 创建项目文件，在项目文件中执行`git init`，初始化 git
2. `git checkout -b <测试环境分支名>`，创建测试环境分支，比如 develop，qa 等
3. 功能迭代切到 main 分支上`git flow feature start <分支名>`，bugfix 在 main 分支上`git flow bugfix start <分支名>`
4. `npm run commit`提交代码，选择相关 type，并输入描述信息
5. 当完成所有功能测试开发，或者 bugfix 之后，通过`git rebase -i`，在 vim 中将除第一个`pick`之外的 commit 都改成`squash`
6. 合并完 commit 后将第一个 commit message 改成此次迭代的功能，或者缺陷修复
7. 将功能分支/bugfix 分支合并到相应测试环境分支，然后创建 release 分支 `git flow release start <分支名>`
8. 在测试环境分支上测试完成之后，将功能分支/bugfix 分支合并到即将发布的 release 分支
9. 完成后将 release 合并到 main 分支
10. `npm run release`为本次发布生成 CHANGELOG，更新版本号，以及打 tag

> 基于 husky+prettier+pretty-quick/lint-staged+eslint 的代码规范

1. [prettier](https://prettier.io/docs/en/index.html)
    - prettier是对于代码格式的约束，比如单双引号，缩进，分号，空格，换行等等
    - 安装使用

    ```
    # 安装prettire
    npm install --save-dev prettier

    # 添加配置文件.prettierrc，参考options自行配置
    {
      "printWidth": 80,
      "trailingComma": "es5",
      "tabWidth": 4,
      "useTabs": false,
      "semi": true,
      "singleQuote": false,
      "bracketSpacing": true,
      "arrowParens": "always",
      "embeddedLanguageFormatting": "auto"
    }
    ```

    - 如果使用 [pretty-quick](https://github.com/azz/pretty-quick)

    ```
    # 安装pretty-quick
    npm install --save-dev pretty-quick

    # 添加配置到husky
    {
        "hooks": {
            ...
            "pre-commit": "pretty-quick --staged",
            ...
        }
    }
    ```

    - 每次 commit 时会按照配置规则对代码自动格式化

2. [eslint](http://eslint.cn/)
    - eslint不光是对代码格式的检查，还是对 [代码规范](http://eslint.cn/docs/rules/) 的检查，比如声明但是未被使用的变量等
    - 安装使用
    ```
    # 安装eslint
    npm install eslint --save-dev
   
    # 添加配置文件.eslintrc
    执行 npx eslint --init 选择相关熟悉生成配置文件
    {
     "env": {
       "browser": true,
       "es6": true
     },
      "extends": "eslint:recommended",
     "rules": {
       // 自定义规则
       // 声明未使用变量时抛错
       no-unused-vars: ["error", "always"],
       // 关闭不能使用console的规则
       no-console: "off",
       // 使用debugger时只发出警告
       no-debugger: "warn"
       ...
     },
     "globals":{
       // 全局变量，比如wx
       wx: true
       ...
     }
    }
    
    # 如果有其他需要可以自行安装相关插件，比如配合prettier
    npm i eslint-config-prettier -D // 安装插件
    "extends": ["eslint:recommended", "prettier"] // 在.eslintrc中添加prettier
   
    # 在package.json中添加命令
    "eslint": "eslint -c ./js-eslint.json src --ext .js" // 指定对src目录下扩展名为js的文件使用js-eslint.json规则
    "eslint-fix": "eslint --fix src --cache --color" // 对src下的文件进行fix，并且缓存修改的文件加快eslint执行
    ```
3. [lint-staged](https://github.com/okonet/lint-staged)
    - 当要对不同类型的文件执行不同的格式化操作时，除了依赖ignore文件之外还可以使用lint-staged
    - 当在 commit 之前要执行多个格式化操作，也可以用lint-staged
    - 比如对ts文件只执行prettier，对js文件执行eslint再prettier
    ```
    # 安装lint-staged
    npm i lint-staged -D
   
    # 添加配置文件.lintstagedrc
    {
      "**/*.js?(x)": ["npm run eslint-fix", "prettier --write"],
      "**/*.ts?(x)": ["prettier --write"]
    }
   
    # 将lint-staged添加到husky
    {
        "hooks": {
            "pre-commit": "lint-staged"
             ...
        }
    }
    
    # 执行 npx mrm lint-staged 可以直接自动添加依赖，并生成配置
    ```
    - 如此在每次 commit 的时候都会执行lint-staged来格式化校验代码，保证代码规范了
    
# 总结

以上就是对于开发中如何规范项目开发流程，以及如何保证代码规范的一个简单介绍
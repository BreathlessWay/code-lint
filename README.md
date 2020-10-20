# GitFlow+Rebase+CommitLint+Standard-Version的开发流程规范

1. [GitFlow](https://www.ruanyifeng.com/blog/2015/12/git-workflow.html) ：简单来说
    - main(即原master)分支为受保护分支，开发者不能直接合并代码进去
    - 每次的开发(比如：功能开发，缺陷修复等)都必须从main切出新分支
    - 根据开发目的的不同切到不同的分支
        - 功能迭代 feature/<分支名>
        - 缺陷修复 bugfix/<分支名>
        - 预发布 release/<分支名>
    - 每次发布测试只能从`功能分支feature/<分支名>`/`缺陷修复分支bugfix/<分支名>`合并到`预发布分支release/<分支名>`
    - 发布上线只能从`预发布分支release/<分支名>`合并到main分支
2. [Rebase](http://jartto.wang/2018/12/11/git-rebase/)
    
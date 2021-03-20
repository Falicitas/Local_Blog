# OI-Wiki资料编写参与

## 基本协作方式

1. Fork 主仓库到自己的仓库中；
2. 当想要贡献某部分内容时，请务必仔细查看 **Issues** ，以便确定是否有人已经开始了这项工作。当然，我们更希望你可以加入 QQ/Telegram 群组以方便交流；
3. 依据 [格式手册](https://oi-wiki.org/intro/format/) 编写内容；
4. 在决定将内容推送到本仓库时，**请首先拉取本仓库代码进行合并，自行处理好冲突，同时确保在本地可以正常生成文档**，然后再将分支 PR 到主仓库的 master 分支上。

## 协作流程

1. 在收到一个新的 Pull Request 之后，GitHub 会给 reviewer 发送邮件；
2. 与此同时，在 [Travis CI](https://travis-ci.org/OI-wiki/OI-wiki) 和 [Netlify](https://app.netlify.com/sites/oi-wiki) 上会运行两组测试，它们会把进度同步在 PR 页面的下方。Travis CI 主要用来确认 PR 中内容的修改不会影响到网站构建的进程；Netlify 用来把 PR 中的更新构建出来，方便 reviewer 审核（在测试完成后点击 Details 可以了解更多）；
3. 在足够多 reviewer 投票通过一个 PR 之后，这个 PR 才可以合并到 master 分支中；
4. 在合并到 master 分支之后，Travis CI 会重新构建一遍网站内容，并更新到 gh-pages 分支；
5. 这时服务器才会拉取 gh-pages 分支的更新，并重新部署最新版本的内容。
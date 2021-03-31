---
title: 安装yarn create nuxt-app xxx时报错
date: 2020-08-30 13:20:09
categories: 报错
tags:
  - 报错
---

如下：

<!-- more -->

```bash
/Users/jimmy/.config/yarn/global/node_modules/sao/lib/installPackages.js:108
throw new SAOError(Failed to install ${packageName} in ${cwd})
^
SAOError: Failed to install packages in /Users/jimmy/work/tuliang/cross-border-pc-ssr-ts
at ChildProcess. (/Users/jimmy/.config/yarn/global/node_modules/sao/lib/installPackages.js:108:15)
at ChildProcess.emit (events.js:315:20)
at maybeClose (internal/child_process.js:1021:16)
at Socket. (internal/child_process.js:443:11)
at Socket.emit (events.js:315:20)
at Pipe. (net.js:674:12) {
__sao: true
}
error Command failed.
Exit code: 1
Command: /usr/local/bin/create-nuxt-app
Arguments: cross-border-pc-ssr-ts
Directory: /Users/jimmy/work/tuliang
Output:
info Visit https://yarnpkg.com/en/docs/cli/create for documentation about this command
```

升级 yarn 到 1.22.4 版本后解决

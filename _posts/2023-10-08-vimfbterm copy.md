---
layout: post
title: NeoVim和fbterm总结：没有GUI和ssh怎么写代码
land: zh-Hans
tags: productivity
---

## 前因后果

小长假期间跳板机的ssh转发功能挂掉了，vscode-remote没办法再用了，但是还有几个程序要调，所以就把NeoVim正经配成了一个IDE。某天又看到自己尘封已久的小破笔记本，竟然还能打开，只是开着图形界面续航实在不忍直视，所以尝试了一下通过fbterm在tty窗口直接开发。

## NeoVim



之前主要在用VsCode，快捷键也是Vim的那一套，所以这次的目的是把除了编辑之外的功能捡起来，比如补全，比如调试，比如文件浏览，比如控制台，基本上就是把NeoVim打造成一个用得惯的VSC。

我用的预配置是[1][LunarVim](https://www.lunarvim.org/)，一些常用的功能已经通过相对丰富的插件支持。LSP的支持带来了不错的补全以及高亮，写码体验尚佳；Copilot的支持也几乎和VSC一致，省去写很多胶水的功夫。
主要的问题在于调试，lvim只预装了DAP，很灵活，但是配置也相对麻烦一点，所以我转向了配置以及调试更傻瓜的[2][Vimspector](https://github.com/puremourning/vimspector)。它的调试和VSC一样，都是基于debugpy，连配置文件都长得很像：
```json
➜  pllm git:(master) ✗ cat .vimspector.json
// .vimspector.json
{
  "configurations": {
    "run": {
      "adapter": "debugpy",
      "default": true,
      "configuration": {
        "request": "launch",
        "type": "python",
        "cwd": "${workspaceRoot}",
        "stopOnEntry": true,
        "program": "${file}",
        "justMyCode": false,
        "env": {
          "PYTHONPATH": "${workspaceRoot}",
          "XXX_LOG_LEVEL": "DEBUG"
        },
        "args": [
          "--config",
          "xxx.yaml",
        ]
      },
      "breakpoints": {
        "exception": {
          "raised": "N",
          "uncaught": "",
          "userUnhandled": ""
        }
      }
    }
  }
}
```
在快捷键上也可以直接将快捷键风格设置成VSC，保证一致的体验。
```
vim.g.vimspector_enable_mappings = 'VISUAL_STUDIO'
```

## FBTerm

FBTerm是单纯想体验一下纯TUI写代码的感觉，让陪了我四年的电脑继续发光发热。TTY不能原生显示中文的原因是UTF8的编码位宽是和ASICI不一样的，在默认的显示上会出问题。有了FBterm以及带有中文字形的字体（比如FiraCode、文泉驿），tty也可以正常显示中文。如果想要在FBTerm同时输入中文呢，可以参考这篇博客
[3][杰哥的小笔记](https://jia.je/misc/2018/07/12/using-fcitx-fbterm/)，需要注意的是fcitx相关的包最好替换成fcitx5。


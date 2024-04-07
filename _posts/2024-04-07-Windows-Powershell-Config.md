---
layout: post
title: Powershell配置记录
---

## Oh-My-Posh
```
winget install JanDeDobbeleer.OhMyPosh -s winget
New-Item -Path $PROFILE -Type File -Force
notepad $PROFILE
add `oh-my-posh init pwsh --config "C:\Users\MZX4\AppData\Local\Programs\oh-my-posh\themes\amro.omp.json" | Invoke-Expression`
```

安装nerd字体
```
oh-my-posh font install
```
选择CaskaydiaMono
在Powershell设置中，默认值-外观-字体，选择`CaskaydiaMono Nerd Font Mono`

## 自动补全 - PSReadLine
```
Install-Module -Name PowerShellGet -Force
Install-Module PSReadLine
```
在profile中增加
```
# 设置预测文本来源为历史记录，就可以自动补全历史命令，类似于zsh的autosuggestions插件
Set-PSReadLineOption -PredictionSource History
#设置emacs模式，就可以像bash一样，按ctrl a跳转命令行开头，按ctrl e跳转命令行尾
Set-PSReadLineOption -EditMode Emacs

#设置按键，可以根据自己使用习惯调整
##ctrl z 撤销
Set-PSReadLineKeyHandler -Key "Ctrl+z" -Function Undo
#ctrl s 菜单补全，和bash的tab类似
Set-PSReadLineKeyHandler -Key "Ctrl+s" -Function MenuComplete
#按方向键补全历史命令
Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward
Set-PSReadLineKeyHandler -Key DownArrow -Function HistorySearchForward
```
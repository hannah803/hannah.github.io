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
选择CaskaydiaMono
```
在Powershell设置中，默认值-外观-字体，选择`CaskaydiaMono Nerd Font Mono`

## 自动补全 - PSReadLine
### ## Powershell 7.4
```
winget install Microsoft.PowerShell -s winget
```
自带自动补全

## VSCode
```
    "terminal.integrated.profiles.windows": {
        "PowerShell_7": {
            "path": "C:\\Program Files\\PowerShell\\7\\pwsh.exe",
            "icon": "terminal-powershell"
          }, 
    }
    "terminal.integrated.defaultProfile.windows": "PowerShell_7",
    "terminal.integrated.fontFamily": "CaskaydiaMono Nerd Font Mono",
```
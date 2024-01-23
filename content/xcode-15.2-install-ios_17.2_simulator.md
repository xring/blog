+++
title = "Xcode 15.2 安装 iOS_17.2_Simulator"
date = 2023-01-23
[taxonomies]
tags=["ios", "xcode"]
+++
更新 Xcode 到 15.2 之后，需要安装iOS_17.2_Simulator，但是在 Xcode 里下载总是网络错误，或者下载卡在 99% 的地方，可以用如下步骤来手动安装。

打开官网 [https://developer.apple.com/download/all/?q=ios](https://developer.apple.com/download/all/?q=ios)，登录后选择下载 `iOS 17.2 Simulator Runtime.dmg` 文件。

假设文件存储到 `~/Downloads`，在终端依次执行下面的命令：
```bash

sudo xcode-select -s /Applications/Xcode.app

xcodebuild -runFirstLaunch 

# 需要替换为自己的路径，下面这条命令需要一些时间来执行
# 执行完成终端会有输出，且会弹窗提示 "Veriyfing..."
xcrun simctl runtime add '/Users/xring/Downloads/iOS_17.2_Simulator_Runtime.dmg'
```

然后在 Xcode 就可以看到 iOS_17.2_Simulator 了。


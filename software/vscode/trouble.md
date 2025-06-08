# 记录vscode使用过程中的一些问题


## 1. 更新失败，路径error
问题描述： 更新vscode时，Bad arguments: Code path doesn't seem to exist: C:\Users\xinpaladin\AppData\Local\Programs\Microsoft VS Code\Code.exe

问题分析： 重装电脑或更改vsocde安装目录时，未更改注册表信息，导致安装目录与实际目录不对应

解决办法：

第一种： 修改VScode注册表信息 
1. win+R 输入regedit
2. 修改 计算机\HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\{771FD6B0-FA20-440A-A002-3B3BAC16DC50}_is1

第二种： 卸载重装，如果不行可以删除第一种方法中的注册表信息，再重新安装
    
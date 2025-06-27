## 踩坑记录

### 问题: 使用 os.OpenFile 并指定权限模式 0666 时，但实际创建的文件权限是 0644
是由于 系统的 umask 设置导致的
##### 1. umask 的作用
umask（用户文件创建掩码） 是一个系统级设置，用于控制新创建文件的默认权限。
在 Linux/Unix 系统中，umask 会屏蔽（扣除）指定的权限位。常见的默认 umask 是 022：
0666（期望权限） & ~022（umask） = 0644（实际权限）。
计算过程：
0666 → rw-rw-rw-
umask 022 → 屏蔽 -w--w----（组和其他用户的写权限）
结果：rw-r--r--（0644）
##### 2. 如何验证 umask？
```
umask # 通常输出 0022 或0002
```
##### 3. 解决方案
在 Go 中手动 chmod（推荐）
创建文件后，显式调用 os.Chmod 强制设置权限：
```
file, err := os.OpenFile("test.txt", os.O_CREATE|os.O_RDWR|os.O_TRUNC, 0666)
if err != nil {
    log.Fatal(err)
}
defer file.Close()

// 强制设置权限为 0666
if err := os.Chmod("test.txt", 0666); err != nil {
    log.Fatal(err)
}
```
# 虚词密码生成器

这是一个用于随机生成由虚词组成的密码的工具。

「虚词」（pesudoword）是指本身不是有意义英语单词、但拼写上符合发音规则的单词。这种单词拥有一定随机性，但更容易被人（短期地）记住。

在家用 WLAN 热点部署过程中，如果使用 [WPA2-Personal（WPA2-PSK）](https://zh.wikipedia.org/wiki/WPA)加密模式，其安全性与密码随机性相关。但过于随机的字符串难以手动输入，因此兼具随机性和「可念性」的虚词密码较为适合作为 Wi-Fi 密码使用。

## 尝试

- 直接前往 <https://pwdgen.astrianzheng.cn>
- 下载项目后运行 `yarn` 和 `yarn serve`，然后按照终端提示操作
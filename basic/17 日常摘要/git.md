## 2021七夕Push上传代码至GitHub时出错误如下：

```
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.
```

意思是远程：2021 年 8 月 13 日移除了对密码身份验证的支持。请改用个人访问令牌。

**第一步**：首先生成自己的token

官方文档:[Creating a personal access token](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token)，注意一定要保存好你生成的token

**第二步**：MAC用户，直接在钥keychain匙串里修改github 的密码为刚刚获得的token

Mac用户建议参考官方文档:[Updating credentials from the macOS Keychain](https://docs.github.com/cn/get-started/getting-started-with-git/updating-credentials-from-the-macos-keychain)

**第三步**：这时候可以`git push` 啦

由于官方文档比较详细，我这里就不自己截图了，也需要大家遇到问题自己上官网寻找满意的答案。


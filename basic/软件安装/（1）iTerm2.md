1、iTerm去[官方下载](https://iterm2.com)，然后安装即可

2、安装ZSH



```
// 查看 Mac 中支持的终端【macOS自带zsh】
cat /etc/shells  
// 修改默认的bash为zsh，重启Terminal
chsh -s /bin/zsh
// 检查修改结果,显示/bin/zsh即成功
echo $SHELL
// 安装后 .zshrc
```

3、oh-my-zsh 安装

```
// 国内地址安装【推荐】
sh -c "$(curl -fsSL https://gitee.com/shmhlsy/oh-my-zsh-install.sh/raw/master/install.sh)"
// 国外地址被墙了
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

4、vim .zshrc   // 打开配置文件

```
打开用户目录下~/.zhsrc，把ZSH_THEME修改为agnoster，保存
plugins=(git zsh-syntax-highlighting)     // 插件之间空格隔开
```

5、字体

```
git clone https://github.com/powerline/fonts.git --depth=1
cd fonts
./install.sh
```

6、安装语法高亮度

```
brew install zsh-syntax-highlighting
```

然后打开`~/.zshrc`，找到`plugins`位置修改为以下内容

```
plugins=(git zsh-syntax-highlighting)
同时在文件末尾添加
source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

7、安装自动补全

这个自动补全的内容是历史输入的命令，使用方向右键`->`补全，使用上述同样的方式安装。

```
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
```

```
plugins=(git zsh-autosuggestions  zsh-syntax-highlighting)
```
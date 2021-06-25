<!--
 * @Author: hsycc
 * @Date: 2021-06-24 18:37:16
 * @LastEditTime: 2021-06-25 14:37:13
 * @Description: vim配置文件及主题
 *
-->

# vim 配置文件及主题

## installation

Downland iTerm2

```sh

# 下载zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

# 安装PowerFonts
pip3 install powerline-status --user
git clone https://github.com/powerline/fonts.git --depth=1  && sh fonts/install.sh && rm -rf fonts
# then to iTerm2 -> Preferences -> Profiles -> Text，font > change Font -> find fonts

# 主题
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

# 配色方案

git clone https://github.com/altercation/solarized && open solarized/iterm2-colors-solarized
# rm this dir

# zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-auto

# echo ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE=\'fg=60\' >> $ZSH_CUSTOM/zsh-autosuggestions_custom.zsh

#zsh-completions
git clone https://github.com/zsh-users/zsh-completions $ZSH_CUSTOM/plugins/zsh-completions

# zsh-syntax-highlighting

git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/custom/plugins/zsh-syntax-highlighting


# git-open
git clone https://github.com/paulirish/git-open.git $ZSH_CUSTOM/plugins/git-open
# brew install git-open


brew install autojump

brew install thefuck





chsh -s /bin/zsh
```

## FIX: iterm 切换 root 没有 zsh 的配置

```sh

echo "source /Users/hsycc/.zshrc" >> ~/.zshrc
source  ~/.zshrc

```

> 出现警告
> [oh-my-zsh] Insecure completion-dependent directories detected:
> lrwxr-xr-x 1 root staff 39 6 18 15:11 /usr/local/share/zsh/site-functions/\_brew -> ../../../Homebrew/completions/zsh/\_brew

```sh 更改文件夹权限及群组
chmod -R 755   /usr/local/share/zsh
chown -R root:staff  /usr/local/share/zsh
```

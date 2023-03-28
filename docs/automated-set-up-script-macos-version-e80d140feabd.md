# 自动设置脚本— macOS 版本

> 原文：<https://medium.com/codex/automated-set-up-script-macos-version-e80d140feabd?source=collection_archive---------12----------------------->

从前有一台 MacBook Pro。有一天，他突然开始故障相机和修复这一点，它去支持。嗯，最有可能发生的情况是:格式化。

因此，为了帮助我和每个需要快速开发 Mac 配置的人，我将描述我选择设置这个脚本的内容。

我们开始吧！

重要的事情先来。让我们安装 Xcode，一个为软件开发者准备的 Mac 工具包。

```
#!/bin/bash

echo "# INSTALLING XCODE"
xcode-select --install
```

第二。**家酿**是一个软件包管理器，让安装 Mac/Linux 应用程序的过程变得容易:

```
echo "# INSTALLING BREW"
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew update
brew -v

echo "# INSTALLING BREW APPS"
brew install --cask google-chrome
brew install --cask steam
brew install --cask insomnia
brew install --cask mysqlworkbench
brew install --cask dbeaver-community
brew install --cask visual-studio-code
brew install --cask intellij-idea
brew install --cask docker
brew install git
brew install docker
brew install mysql
brew install nvm
brew install bash-completion
```

第三。我们需要 SDKMAN！为开发套件经理。

```
echo "# INSTALLING SDKMAN"
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
```

第四。嗨嗨。用一点胶水把所有东西粘在一起。

```
echo "# CREATE AND ADDING SCRIPTS TO .BASHRC AND .BASH_PROFILE FILE"
echo "export PATH=$PATH:/usr/local/bin
source $(brew --prefix nvm)/nvm.sh

# The following line is added by pre-commit
export PATH='/Users/ssin/Library/Python/3.9/bin:$PATH'

# GIT BRANCH IN PROMPT
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}
export PS1='\[\033[32m\]\w - \$(parse_git_branch)\[\033[00m\] $ '

if [ -f $(brew --prefix)/bash_completion.d/kubectl ]; then
. $(brew --prefix)/bash_completion.d/kubectl
fi

# THIS LOADS NVM
export NVM_DIR=$HOME/.nvm
[ -s '$NVM_DIR/nvm.sh' ] && \. '$NVM_DIR/nvm.sh'
[ -s '$NVM_DIR/bash_completion' ] && \. '$NVM_DIR/bash_completion'

# THIS MUST BE AT THE END OF THE FILE FOR SDKMAN TO WORK!!!
export SDKMAN_DIR='$HOME/.sdkman'
[[ -s '$HOME/.sdkman/bin/sdkman-init.sh' ]] && source '$HOME/.sdkman/bin/sdkman-init.sh'
" >> ~/.bashrc
source ~/.bashrc

echo "
if [ -e ~/.profile ]; then . ~/.profile; fi

if [ -r ~/.bashrc ]; then
   source ~/.bashrc
fi" >> ~/.bash_profile
source ~/.bash_profile

echo "# DONE"
```

第五。我选择安装这些节点版本。

```
echo "# INSTALLING NODE VERSIONS /n"
nvm install v10.13.0
nvm install v10.18.1
nvm install v10.24.1
nvm install v14.17.5
nvm install 14.19.3
nvm alias default node v14.19.3
nvm use 14.19.3
echo "# NPM VERSION:"
npm -v
```

对于 Java 来说，这个可能会有所帮助。

```
echo "# INSTALLING JAVA VERSIONS"
sdk install java 8.0.342-amzn
sdk use java 8.0.342-amzn
```

第六。让我们配置 git。

```
echo "# GIT CONFIG"
git config --global user.name "Your Name"
git config --global user.email "yourname@email.com"
git config --list
```

加上一些 VSCode 和 iTerm 的扩展和配置。

```
echo "# INSTALLING VSCODE EXTENSIONS"
code --install-extension dbaeumer.vscode-eslint
code --install-extension esbenp.prettier-vscode
code --install-extension azemoh.one-monokai
code --install-extension vscode-icons-team.vscode-icons
echo "# THESE ARE THE INSTALLED EXTENSIONS:"
code --list-extensions
```

*安装 VSCode 扩展的任何问题，[请查看此处](https://github.com/microsoft/vscode/issues/141738)。

```
echo "# DON'T FORGET TO UPDATE SETTINGS.JSON VSCODE FILE AND ITERM2 FOR PERSONAL CONFIGS"
```

将所有内容保存到一个名为`myscript.sh`的文件中。

之后，我们可以简单地在终端上运行:

```
sh myscript.sh
```

我们准备好了。你可以在这里找到整个脚本+个人配置[。](https://github.com/scsin/configs)

***愿剧本与你同在！***

***演职员表***

> [我如何设置新的开发机器](https://www.youtube.com/watch?v=kIdiWut8eD8&t=1s)
> [如何设置你的 Mac 进行开发](/@maxy_ermayank/developer-environment-setup-script-5fcb7b854acc)
> [在一个脚本中设置你的开发机器](https://skofgar.ch/computer-science/2020/04/setting-up-your-dev-machine-in-one/)
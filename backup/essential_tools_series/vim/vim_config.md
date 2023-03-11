# vim 配置文件

建议打好基础，先理解好 vim 基础操作及概念，然后在使用的过程中，逐步完善 vim 的配置，这样才能更好地驾驭它。

## 编写 vim 配置

### vimrc 文件持久化保存 vim 相关配置
- Linux/Unix 下，用户在自己主目录下，新建一个隐藏文件 vim ~/.vimrc
- Window 下，vim $MYVIMRC ，通过环境变量来编辑配置文件

### vimrc 文件包含内容

- 常用设置
  - set number 设置行号
  - colorscheme hybrid 设置 hybrid 配色主题
- 常用 vim 映射
  - `noremap <leader>w :w<cr>` 保存文件
- 自定义的 vimscript 函数和插件的配置

#### 常用设置

- normal 模式下，通过 :help option-list 可以浏览所有的选项，然后用 / 查询，使用 :q 退出

#### vim 映射快捷键

vim 中的映射比较复杂，源于 vim 有多种模式，但是其功能很强大
- 设置一下 leader 键，let mapleader = "," 常用的是逗号或着空格

#### vim 插件

- 现代化的 vim 可以通过插件管理器 vim-plug 安装插件

#### vim 脚本

- vim 有自己的脚本语言 vimscript

> [!TIP]
> - 可以研究下 vim-go-tutorial 中的 vimrc 文件
> [vim-go](https://github.com/fatih/vim-go) 、 [vim-go-tutorial](https://github.com/fatih/vim-go-tutorial)
> - 可以参考其他人开源的配置文件 dotfiles
> - 通过 git 把 vimrc 等相关配置同步到电脑上

## vim 映射

vim 的映射，就是把一个操作，映射到另一个操作上

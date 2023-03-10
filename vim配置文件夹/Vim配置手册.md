# vim配置手册说明

**【说明】：该手册根据个人需求以及网页参考制成。如有能力，尽量参考原英文文档：`https://www.freecodecamp.org/news/vimrc-configuration-guide-customize-your-vim-editor/`**，感谢原作者的分享。如果本文有所缺漏，应该是本文作者理解问题，请参考原文自行更改。部分内容根据自己个人的理解，进行了增删。

## 基本配置

### 准备工作
- 在你的家目录下创建如下目录形式:`$ mkdir -p ~/.vim ~/.vim/autoload ~/.vim/backup ~/.vim/colors ~/.vim/plugged`


		.vim/
		├── autoload/
		├── backup/
		├── colors/	 
		└── plugged/


- 在你的个人家目录下创建一个`.vimrc`文件:`$ touch ~/.vimrc`
- **【注】：在个人目录配置的好处在于只对该登录用户起作用，对于其他用户则不受影响。如果这台设备属于你个人完全支配，那么在主目录下设定也无不可。**

### 基本设置

- 添加以下代码在你的`.vimrc`文件中：


		" 禁用可能导致vim一场的功能
		set nocompatible
		
		"添加鼠标功能，个人还是喜欢用鼠标的
		set mouse=a
		
		" 启用类型文件检测。Vim将能够尝试检测正在使用的文件类型。
		filetype on
		
		" Enable plugins and load plugin for the detected file type.
		filetype plugin on
		
		" Load an indent file for the detected file type.
		filetype indent on

- **语法高亮功能**：在`.vimrc`文档尾部追加，以下内容:

		" 打开语法高亮.
		syntax on

- **在每行行首显示行号**：继续在文档尾部追加

		" 在每行最左侧添加行号.也可以写成set nu
		set number

- **高亮当前鼠标的位置。高亮鼠标所在的行以及列**：继续在尾部追加以下代码

		" 高亮鼠标所在的行.
		set cursorline
		
		" 高亮鼠标所在的列.
		set cursorcolumn

- **为增强编程体验，将以下行追加到文件末尾：**
		
		" 设置shift的宽度为4个空格
		set shiftwidth=4
		
		" 设置Tab的大小为4个。这里设置的原因是，在有的系统里Tab的大小是2个空格
		set tabstop=4
		
		" 用空格代替Tab，也就是说Tab会被替换为空格
		set expandtab
		
		" 不保存备份文件
		set nobackup
		
		" 鼠标滚动时不要让光标滚动到N行以下或以上（本处设置为10行）。
		set scrolloff=10
		
		" 不换行，允许行尽量长。
		set nowrap
		
		" 在搜索文件时，当你输入时，递增地高亮显示匹配的字符。
		set incsearch
		
		" 搜索时忽略大写字母。
		set ignorecase
		
		" 如果搜索大写字母，重写ignorecase选项。
		" 这将允许你专门搜索大写字母。
		set smartcase
		
		" 显示你在屏幕最后一行输入的部分命令。
		set showcmd
		
		" 在最后一行显示你的模式。
		set showmode
		
		" 在搜索过程中显示匹配的单词。
		set showmatch
		
		" 搜索时使用高亮显示。
		set hlsearch
		
		" Use highlighting when searching.
		set history=1000

- **自动补齐功能**：Bash补全是一个很好的特性，它通过自动补全您键入的内容来节省击键。Vim有一个类似的功能，称为wildmenu。

		" 按下Tab后自动完成补齐。其实vim编辑界面下，按下ctrl+N也是可以自动补齐的。
		set wildmenu
		
		" 使wildmenu的行为类似于Bash补全。
		set wildmode=list:longest
		
		" 有些文件是我们永远都不想用Vim编辑的。
		" Wildmenu将忽略具有这些扩展名的文件。
		set wildignore=*.docx,*.jpg,*.png,*.gif,*.pdf,*.pyc,*.exe,*.flv,*.img,*.xlsx

### 在vim中折叠/收起多行：你可以理解为将某个函数收起（fold）

- **添加如下代码到文件末尾**：

		" 插件 ---------------------------------------------------------------- {{{
		
		" 插件的代码放在这里.
		
		" }}}
		
		
		" 映射 --------------------------------------------------------------- {{{
		
		" 映射的代码放在这里.
		
		" }}}
		
		
		" vim脚本 -------------------------------------------------------------- {{{
		
		" 以下代码会使代码段折叠.
		" 使用标记法折叠.
		augroup filetype_vim
		    autocmd!
		    autocmd FileType vim setlocal foldmethod=marker
		augroup END
		
		" 更多的Vim脚本放在下面.
		
		" }}}
		
		
		" 状态行 ------------------------------------------------------------ {{{
		
		" 状态条代码放在这里.
		
		" }}}

- 写完这些，按下`ESC`，退出编辑模式，输入`:w`将文件保存。下面简单介绍折叠代码的用法：
	- `zo`在光标处打开一个折叠
	- `zc`来关闭光标处的折叠
	- `zR`打开所有折叠
	- `zM`关闭所有折叠。

### 如何在vim中添加插件

- 你可以向Vim添加插件以添加额外的功能。大多数人使用**插件管理器**来简化插件安装。我们可以使用各种各样的插件管理器。我将向你展示如何安装和使用vim-plug插件管理器。
- 在Linux或Mac OS上按照如下方式安装vim-plug插件：
		
		$ curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
		    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim

- 在plugins部分中添加`call plug#begin('~/.vim/plugged')`和`call plug#end()`行。我们安装的插件代码将添加在这两个函数调用之间。

		" PLUGINS ---------------------------------------------------------------- {{{
		
		call plug#begin('~/.vim/plugged')
		
		
		
		
		call plug#end()
		
		" }}}


现在安装插件就像在函数调用之间添加你在GitHub上找到的Plug 'username/plugin-name'字符串一样简单。

- 下面介绍如何安装NERDTree（可以在编辑器左侧多显示一个窗口，用于显示文件目录）和 Asynchronous Lint Engine (ALE)（提供检测(语法检查和语义错误)）。将下面这两行添加到两个调用plug#…行之间:

		" PLUGINS ---------------------------------------------------------------- {{{
		
		call plug#begin('~/.vim/plugged')
		
		
		  Plug 'dense-analysis/ale'
		
		  Plug 'preservim/nerdtree'
		
		
		call plug#end()
		
		" }}}

按下`ESC`，退出编辑模式，再输入`：w`，将文件保存。在Vim下输入`:PlugInstall`下载并安装这两个插件。

**个人觉得以上的内容基本上就够用了，作者还添加了一些别的功能，详见附件。**
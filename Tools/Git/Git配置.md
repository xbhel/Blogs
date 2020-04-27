### 配置 Git 选项

> 配置一些 Git 的选项，便于在后面能够更好的给你一些提示 !

在使用 `Git` 时，将会设置两种配置 ：

- 全局配置 ：应用于所有的仓库，配置文件保存在 `~/.gitconfig` 
- 本地配置 ：只应用于当前仓库，配置文件保存在当前仓库的 `.git/config`

#### 查看配置

查看某个**设置过**的配置项，如用户名 ：

`$ git config --get user.name` 

查看所有**设置过**的配置项 ：

`$ git config --list`

查看所有配置项 :

`$ git help config`

其实就是打开该目录下的文档 `Git/mingw64/share/doc/git-doc/git-config.html`

#### 表明身份

通过配置用户名和邮箱来告诉 `Git` 你是谁 ！

全局配置 ： 

`$ git config --global user.name 'Your name'`

`$ git config --global user.email 'me@example.com'`

本地配置，有时候需要在一些特定的仓库使用个性化的信息 ：

- 前往需要配置的仓库目录
- 用 `--local` 代替 `--global` 并应用配置命令即可

如：`git config --local user.email 'me@example.com'`

#### 更改编辑器

默认情况下，`Git` 会使用系统编辑器，一般是 Vim ，但它有些难以驾驭，因此你可能希望更改编辑器，运行以下命令查看 `Git` 当前使用的编辑器.

`$ git config --get core.editor`

如果你要使用 `Subline`，请使用以下命令 ：

`$ git config --global core.editor subl -n -w`

在windows更改编辑器，则需要加入应用文件的完整路径，你需要在应用路径两侧加上双引号，此外，每当你使用 Bash 来调用 `git config` 时，必须给值加上引号，这会导致出现了一个双重引号的字符串，如下所示 ：

`$ git config --global core.editor '"C:\Program Files\Vim\gvim.exe"' ` 

#### 添加颜色

阅读一大段文本是很费劲的，可以在命令行中添加辅助颜色，以便更容易看清楚 `Git` 在做什么 !

`$ git config --global color.ui true`

`$ git config --global diff.ui auto`

#### 自定义命令提示符

通过自定义命令提示来帮助你查看文件的状态，如你当前选择的分支，以及你是否修改了仓库中的文件，这是一个常见的小技巧，上网搜索 `bash prompt git status` 将会返回大量结果

#### 忽略系统文件

`Git` 允许你设置一个全局忽略文件来避免将某些文件提交到仓库,你可以在 `https://github.com/github/gitignore`查看常用忽略文件清单,挑选最适合你系统和项目的列表，一旦你选好了想要忽略文件的列表，请完成以下步骤 ：

- 创建一个新的文本文件，名为 `.gitignore_global`,将它放在你的home目录下
- 通过运行以下命令告诉 `Git` 使用这个配置

`$ git config --global core.excludesfile ~/gitignore_global`

或与你在**特定的仓库**有一些额外的文件需要忽略不想提交到仓库，在每个仓库中，你都可以创建一个自定义的"忽略"文件，进一步限制 `Git` 要跟踪的文件，步骤如下 ：

- 创建一个新的文本文件，名为 `.gitignore`，将它放在你的项目根目录下.
- 在这个文件中添加所有你希望 `Git` 永远不要添加到仓库的文件名称，每个文件名应该单独一行，你也可以使用模式匹配.例如 `*.swp`代表临时编辑器的文件.

这个更改需要在 `Git` 仓库中创建一个新的提交，如下所示 ：

`$ git add .gitignore`

`$ git commit -m 'Adding list of to be ignore'`
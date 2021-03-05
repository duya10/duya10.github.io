# Visual Studio Code 使用手册


## Visual Studio Code 使用手册

### 设置中文

1）在插件商店下载插件 *Chinese (Simplified) Language Pack for Visual Studio Code*，install and reload

2）【Ctrl + Shift + p】搜索 configure display language，打开 locale.json 文件

3）修改 locale.json 文件，*"locale":"zh-cn"*

4）重启 vscode

### vscode 设置同步

下载插件 *Settings Sync* 并安装重载

#### 上传配置

##### 从 GitHub 上获取 Personal Access Token

**[Settings](https://github.com/settings/profile) / [Developer settings](https://github.com/settings/tokens) / [Personal access tokens](https://github.com/settings/tokens) / Generate New Token**

![Generate token](images/vscode_token.png)

![Genetate token](images/vscode_token_2.png)

获取 access tokens

> 保存 Token 以备使用（从其他计算机上传配置需要）

![Personal access tokens](/images/vscode_token_3.png)

#### 第一次上传配置

在控制面板中输入 **"Sync : Upload"**

> 快捷键 Shift + Alt + u

这会自动打开你的 GitHub Settings 界面，允许你为应用程序生成新的 token，这个token 允许这个插件生成 gist

输入 GitHub Token

![token](/images/vscode_token_4.png)

插件会自动上传配置并提供 **GIST ID**

> 保持 Gist ID 以便在其他计算机下载我们上传的配置

需要 Gist ID 才能访问你使用 GitHub Token 上传的数据。复制此 Gist ID 以便其他计算机可以下载设置。

![Gist ID](/images/vscode_sync.png)

执行 **upload** 命令的完整过程

![upload](/images/vscode_sync.gif)

### 下载配置

在控制面板输入 **"Sync : Download"**，程序会请求你的 **GitHub Gist ID**

> 快捷键 Shift + Alt + d

输入 GitHub Token

![Token](/images/vscode_sync_2.png)

输入 Gist ID，下载上传过的配置

![Gist ID](/images/vscode_sync_3.png)

配置下载完成，重新启动 VSCode 以使用已安装的插件

![download](/images/vscode_sync_4.png)

执行 **download** 命令的完整过程

![download](/images/vscode_sync_2.gif)

### 重置 Token / GIST Settings

在控制面板输入 **Sync : Rest** 重置设置

### 高级选项

控制面板输入 **Sync : Advanced** 可以查看更多高级选项

> GitHub Token: d2a4cf35819d8b15da88f2eb0576d65443c7d087  
> GitHub Gist ID: ac136f45652095a7a423e151811fb16e


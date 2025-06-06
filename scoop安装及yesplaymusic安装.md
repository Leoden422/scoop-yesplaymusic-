## windows Scoop 安装

### **一、什么是 Scoop？为什么用它？**

***

Scoop 是 Windows 上的命令行软件包管理器安装应用程序，Scoop 是一个轻量且强大的选择。类似手机的 “应用商店”。用它装软件的好处：

- **无需管理员权限**：软件默认装在用户目录（如`C:\Users\你的用户名\scoop`），避免 UAC 弹窗。
- **干净卸载**：卸载时自动删除所有文件，不留垃圾。
- **一键更新**：`scoop update`命令就能更新所有软件。

### **二、安装前的准备工作**

***

#### 1. 工具准备

​	1.Windows 10/11

​	2.需要GitHub的账号

​	3.魔法工具，或者开发者辅助工具，我这里推荐：开发者辅助工具**dev-sidecar**，原因是好用开源免费。

#### 2. 启用 PowerShell 执行策略

按`Win + X`，选 “Windows PowerShell (管理员)”，打开后执行以下命令（复制粘贴回车）。

==全文的命令需要一条一条执行==。

```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
Get-ExecutionPolicy 
 # 应输出：RemoteSigned
```

**原理说明**：PowerShell 默认限制脚本执行，`RemoteSigned` 允许运行本地脚本和签名的远程脚本，是安全与便利的平衡选择.是安装和使用 Scoop 的必要步骤，它允许 PowerShell 执行从网络下载的脚本，同时保证了一定的安全性。在日常使用中，这是一个安全且实用的执行策略设置。

#### 3. 配置环境变量（指定 Scoop 安装路径）

```powershell
#在 使用mkdir命令自定义创建路径，例子C:\scoop：。(Ps:一定是用管理员身份打开PowerShell，输入指令。)
mkdir  C:\scoop 

# 设置 SCOOP 环境变量为 C:\scoop（自定义安装路径）
$env:SCOOP='C:\scoop'

# 永久保存环境变量到当前用户配置
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')

# 验证环境变量是否设置成功
echo $env:SCOOP  # 应输出：C:\scoop
```

**关键点说明**：

- 通过 `$env:SCOOP` 临时定义变量，仅在当前会话有效
- 使用 `.NET Environment` 方法将变量写入注册表，实现永久生效
- 自定义路径可避免系统盘空间占用，或满足权限管理需求

### 三、安装 Scoop

***

在刚才的 PowerShell 窗口中，复制粘贴下面的命令：

```powershell
irm get.scoop.sh | iex
```

安装完成后，验证是否成功：

```powershell
scoop help
```

如果看到帮助信息，说明安装成功！

#### **添加额外软件源（Extras 仓库）**

```powershell
# 添加 extras 仓库（包含更多非官方应用）
scoop bucket add extras
```

到此，Scoop就安装完成。

#### Scoop 常用管理命令

| 命令                         | 作用            |
| ---------------------------- | --------------- |
| `scoop update`               | 更新 Scoop 本体 |
| `scoop update [app name]`    | 更新指定应用    |
| `scoop uninstall [app name]` | 卸载应用        |
| `scoop search [keyword]`     | 搜索应用        |
| `scoop status`               | 查看过时应用    |
| `scoop cache clear`          | 清除安装包缓存  |

## 举例安装yesplaymusic

### 一、 YesPlayMusic播放器是什么？

***

YesPlayMusic 是一款开源的第三方网易云音乐客户端。

**优势**：

- **界面美观简洁**：采用现代设计理念，界面简洁大方，没有繁琐的广告、直播和社交功能干扰，专注于音乐播放体验。
- **功能丰富实用**：
  - **多种播放功能**：支持 MV 播放、歌词显示、私人 FM、每日推荐歌曲、音乐云盘等功能，能满足用户多样化的音乐需求。
  - **账号登录与同步**：支持扫码、手机和邮箱等网易云账号登录方式，可同步用户在网易云音乐的歌单、收藏、播放历史等数据。
  - **海外访问支持**：海外用户登录网易云账号后可直接播放。
  - **自动签到功能**：每日自动进行手机端和电脑端的签到，帮助用户轻松获取网易云音乐的积分，提升用户在网易云平台上的权益。

### 二、 安装前的准备工作

***

​	工具准备：

- scoop安装完成
- 自下载7-ZIP解压包，用来解压
- 下载extras 仓库

### 三、开始安装

***

#### 1.下载命令：

YesPlayMusic 在`extras`这个 “仓库” 里，添加它：

```powershell
scoop bucket add extras
```

**可能的报错处理**：

- 如果提示`Failed to download...`，可能是网络问题，多试几次或挂代理。
- 如果提示`Bucket 'extras' already exists`，说明已添加过，跳过这步。

#### 2. 搜索 YesPlayMusic

执行搜索命令，确认能找到：

```powershell
scoop search yesplaymusic
```

正常会看到类似这样的输出：

```plaintext
extras/yesplaymusic (版本号)
  A third-party网易云音乐播放器...
```

#### 3. 开始安装

复制粘贴命令：

```powershell
scoop install yesplaymusic
```

**安装过程解析**：

1. **自动依赖安装**：Scoop 先安装 7zip（用于解压应用包）
2. **下载应用程序**：从 extras 仓库获取 YesPlayMusic 安装包
3. 文件处理流程：

   - 验证安装包哈希值（确保文件完整性）
   - 解压安装包到 `C:\scoop\apps\yesplaymusic\版本号`
   - 创建软链接到 `current` 目录（便于统一调用）
   - 生成快捷方式和命令行 Shim（实现 `yesplaymusic` 命令调用）

**安装成功验证**：

```powershell
# 查看已安装应用列表
scoop list

# 输出示例：
Installed apps:

Name         Version Source Updated             Info
----         ------- ------ -------             ----
7zip         24.09   main   2025-06-06 10:56:40
yesplaymusic 0.4.8-2 extras 2025-06-06 10:56:52
```

#### 2.常见问题与解决方案

##### **2.1. 命令无法识别问题**（一般情况不用）

```powershell
# 问题现象
yesplaymusic : 无法将“yesplaymusic”项识别为 cmdlet...

# 解决方案 1：检查路径是否添加到环境变量
$env:PATH -split ';' | Select-String 'scoop'

# 预期输出应包含 C:\scoop\shims（Scoop 命令目录）

# 解决方案 2：手动刷新环境变量
$env:PATH = [Environment]::GetEnvironmentVariable('PATH', 'User') + ';' + 
             [Environment]::GetEnvironmentVariable('PATH', 'Machine')
```

**原理**：Scoop 会自动将 `C:\scoop\shims` 添加到 PATH，但有时需要手动刷新或重启终端生效。

##### **2. 2手动定位应用路径**

```powershell
# 查看应用实际安装路径
scoop prefix yesplaymusic  # 输出：C:\scoop\apps\yesplaymusic\current
```

**使用场景**：

- 需直接访问应用目录（如查看配置文件）
- 手动创建桌面快捷方式时获取可执行文件路径

#### 4. 验证安装

安装完成后，执行：

```powershell
yesplaymusic
```

如果弹出播放器界面，恭喜你！安装成功！

### **五、后续使用技巧**

#### 1. 启动播放器

- 方法 1：在开始菜单搜索 “YesPlayMusic”
- 方法 2：在 PowerShell 中输入`yesplaymusic`

#### 2. 更新播放器

```powershell
scoop update yesplaymusic
```

#### 3. 卸载播放器

```powershell
scoop uninstall yesplaymusic
```

#### 4. 查看已安装软件列表

```powershell
scoop list
```


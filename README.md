# WineGame on Flatpak

This is a customized version of Lutris for Chinese users, with [an independent server](https://winegame.net/home) and [installable games](https://winegame.net/games/). Because the service is for Chinese users, we only have Simplified Chinese documents.

Wine游戏助手是 [Lutris](https://github.com/flathub/net.lutris.Lutris) 的中国定制版，有[单独的服务器](https://winegame.net/home)和[可安装游戏列表](https://winegame.net/games/)。因为服务仅面向中国用户，我们只有简体中文文档。

Wine游戏助手和Lutris一样，可以让你在Linux中轻松启动Windows游戏，并且还为非x86平台（比如ARM64、龙芯龙架构等）提供了支持（详见[下载页面](https://winegame.net/downloads/)）。不过目前flatpak版仅限x86，未来可能扩展到其他平台。

该版本库用于打包Wine游戏助手的[Flatpak](https://flatpak.org)版。

## 安装

建议遵循这个安装教程，更新更及时：https://hu60.cn/q.php/bbs.topic.103194.html

如果你一定要看这里的文档，那么：

1. 添加 Flathub 和 WineGame 软件仓库
   ```sh
   sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
   sudo flatpak remote-add --if-not-exists winegame https://file.winegame.net/flatpak/repo/winegame.flatpakrepo
   ```

2. 安装 GNOME Compat 和 GL32 扩展
   ```sh
   sudo flatpak install flathub org.gnome.Platform.Compat.i386//42 org.freedesktop.Platform.GL32.default//21.08 org.freedesktop.Platform.GL.default//21.08
   ```

   **不要混淆** `org.gnome.Platform.Compat.i386` 和 `org.freedesktop.Platform.Compat.i386`，它们不一样。

3. 安装Wine游戏助手
   ```sh
   sudo flatpak install winegame net.winegame.client
   ```

## 运行
从应用程序列表中的“Wine游戏助手 (flatpak)”图标启动，或者在终端运行如下命令：
```
flatpak run net.winegame.client
```

## 构建

建议在 Arch Linux 中构建，其他发行版如果 flatpak-builder 版本太低，可能会构建失败。

1. 安装 [Flatpak](https://flatpak.org/) 和 [Flatpak Builder](http://docs.flatpak.org/en/latest/flatpak-builder.html)，Arch Linux 的安装方法如下：

   ```
   sudo pacman -S flatpak flatpak-builder
   ```

2. 克隆当前版本库并`cd`进去。

3. 同步 git 子模块：
   ```sh
   git submodule init
   git submodule update
   ```

4. 编译 flatpak：
   ```sh
   sudo flatpak-builder --repo=./lutris --force-clean --install-deps-from=flathub ./build-dir net.winegame.client.yml -v
   ```

5. 从构建文件夹安装：
   ```sh
   sudo flatpak remote-add lutris ./lutris --no-gpg-verify
   sudo flatpak install lutris net.winegame.client
   ```

6. 导出 flatpak 打包：
   ```
   flatpak build-bundle ./lutris ./net.winegame.client.flatpak net.winegame.client
   ```

7. 安装 flatpak 打包：
   ```
   sudo flatpak install ./net.winegame.client.flatpak
   ```

### MangoHud

运行以下命令安装 MangoHud 插件：

```
sudo flatpak install flathub org.freedesktop.Platform.VulkanLayer.MangoHud
```

### GameScope

运行以下命令安装 GameScope 插件：

```
sudo flatpak install flathub com.valvesoftware.Steam.Utility.gamescope
```

## 已知问题

- [32位支持库无法自动安装](https://github.com/flathub/net.lutris.Lutris/issues/53)

- [某些应用程序无法正常读取 ~/Games 文件夹](https://github.com/flathub/net.lutris.Lutris/issues/89)

   以上都可以通过手动安装32位支持库解决
   ```sh
   sudo flatpak install flathub org.gnome.Platform.Compat.i386//42 org.freedesktop.Platform.GL32.default//21.08 org.freedesktop.Platform.GL.default//21.08
   ```

- Wine游戏助手无法读取我的自定义游戏文件夹

   相关错误报告：[#79](https://github.com/flathub/net.lutris.Lutris/issues/79)

   你需要手动授予访问权限：
   ``` sh
   sudo flatpak override --filesystem=/path/to/你的游戏文件夹 net.winegame.client
   ```

- 由于缺少权限，英雄联盟和WeGame无法启动

   相关错误报告：[#85](https://github.com/flathub/net.lutris.Lutris/issues/85)

   无解，除非修改 flatpak 才能加回该权限。

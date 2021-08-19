# 关于M1芯片的MAC完美使用Emby-Theater-Electron的方法

相信有部分M1用户在使用Emby-Theater-Electron (以下简称ETE) 的时候都出现过服务器的影视库加载正常，却没有办法正常播放视频，且表现为无限加载+网速为0的情况，然而在外挂了播放器 (如IINA) 后却能正常播放

## 原因分析：

由于 ETE 需要**调用mpv视频播放器**对 HEVC 视频内容进行解码，因此需要在使用 Homebrew 来安装 mpv 播放器。但是由于 Homebrew 修改了对M1芯片的Mac安装路径，从原来的** `/usr/local/Homebrew `** 变成了 **`/opt/homebrew`**。而 ETE (latest version 3.0.15) 依然不支持新的路径，也就导致了 ETE 无法播放视频，表现为在视频无限加载却没有速度

## 配置&解决方法：

在 M1 的 Mac下加装一个 x86 下的 Homebrew ，在**`/usr/local`**的路径下安装 mpv 播放器，方法来源 [@PlatyHsu](https://sspai.com/post/63935)

同时感谢 [@rartv](https://github.com/rartv/EmbyPublic/tree/test/Emby%20Theater) 提供的对于 mac 配置 ETE 的编译与教程

- ### 下载并安装ETE

  下载 [EmbyTheater.zip](https://github.com/rartv/EmbyPublic/tree/test/Emby%20Theater) 解压出**`Emby Theater.app`**文件，拖到**`Application(应用程序)`**文件夹中

  应用源码：*https://github.com/thura10/emby-theater-electron*

- ### 安装mpv播放器

  1. 首先我们要在 M1 的 mac下安装 x86 版本的 Homebrew

     进入 x86 模式：

     ```
     arch -x86_64 $SHELL
     ```

     这样就可以启动一个 x86 模式的终端，使得之后的命令都在 x86 模式下运行

     此时，使用 Homebrew 官方安装脚本 (如有网络连接问题请自行换源)

     ```
     /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
     ```

     这样就可以完成 x86 模式的 Homebrew 的安装了

  2. 在 x86 的 Homebrew 下安装 mpv 播放器

     如果你的电脑上面安装了两个不同版本不同路径的 Homebrew 推荐在使用 x86 版本的 Homebrew 时使用此命令**`arch -x86_64 /usr/local/bin/brew`**

     因此，如果要在 x86 的 Homebrew 路径下安装 mpv 播放器，请输入

     ```
     arch -x86_64 /usr/local/bin/brew install --HEAD mpv
     ```

- ### 替换 libass，使字幕显示完美

  由于上面安装的 `libmpv` 对于某些中文字幕会乱码，所以给出一个解决办法。( 原因: https://zrstea.com/261/ )

  1. 下载 [libass.rb](https://github.com/rartv/EmbyPublic/releases/download/0.0.33/libass.rb) 这个文件

  2. 进入下载目录，假设为 **`~/Downloads`**，那么就在终端输入 **`cd ~/Downloads`**

  3. 卸载 x86 下安装 `libmpv` 时安装的 `libass` ，然后重新编译并安装，命令如下，请分开执行2次指令

     ```
     arch -x86_64 /usr/local/bin/brew uninstall libass --ignore-dependencies && arch -x86_64 /usr/local/bin/brew install fontconfig 
     ```

     ```
     arch -x86_64 /usr/local/bin/brew install -s libass.rb
     ```

     *以上三个步骤源方案来至: [@PANINI](https://t.me/PAN1N1) 和 [@xinzhe he](https://t.me/hexinzhe)*

- ### 客户端解锁

  1. 在**`Application(应用程序)`**文件夹中找到 **`Emby Theater.app`**，右键 **`show contens(显示内容)`**。找到**`Contents/Resources/app/main.js`**文件，用编辑器 (VScode) 打开

  2. 用 **`cmd+f`** 搜索找到

     ```
         function getAppBaseUrl() {
     
             var url = 'https://tv.emby.media';
     
             //url = 'http://localhost:8088';
             return url;
         }
     ```

     替换为

     ```
         function getAppBaseUrl() {
     
             var url = 'https://tv.emby.neko.re';
     
             //url = 'http://localhost:8088';
             return url;
         }
     ```

     保存即可正常享用

     *解锁方法来源：https://neko.re/archives/225.html*


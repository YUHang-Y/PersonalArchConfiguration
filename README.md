# 🛠️ **Arch Linux 包管理与 Hyprland 安装指南**

本指南详细介绍如何在 Arch Linux 上配置清华大学 archlinuxcn 镜像源、安装 Hyprland 桌面环境、配置输入法（fcitx5）、终端美化（Powerlevel10k），以及使用 **pacman**（官方包管理器）和 **paru**（AUR 助手）进行软件安装、更新、搜索和系统维护。包含实用示例和技巧。

---

## 🖥️ **一、配置清华大学 Arch Linux 中文社区仓库**

为确保安装 Hyprland 和其他软件时能获取更多资源（如中文输入法），建议配置清华大学 archlinuxcn 仓库。

1. **编辑 pacman 配置文件**  
    在 `/etc/pacman.conf` 文件末尾添加以下两行（使用清华大学镜像源）：
    
    ```bash
    [archlinuxcn]
    Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
    ```
    
2. **安装并导入 GPG 密钥**  
    同步仓库并安装 `archlinuxcn-keyring` 包：
    
    ```bash
    sudo pacman -Sy archlinuxcn-keyring
    ```
    

---

## 🖼️ **二、安装 NIRI 桌面环境**

以下是通过一条命令链快速安装 NIRI 的步骤：

### 命令

```bash
bash <(curl -L is.gd/shorinsetup)              #部署快捷键,软件
curl -fsSL https://install.danklinux.com | sh  #部署桌面
```

### 步骤说明

1. ~~克隆 JaKooLit 的 Arch-Hyprland 仓库到 `~/Arch-Hyprland`（限制克隆深度以加快速度）。~~
2. ~~进入 `~/Arch-Hyprland` 目录。~~
3. ~~为 `install.sh` 脚本添加可执行权限。~~
4. ~~执行 `install.sh` 脚本以安装和配置 Hyprland。~~

---

## 🔠 **三、配置输入法（fcitx5）**

为支持中文输入，安装并配置 fcitx5 输入法框架。

```bash
# 安装 fcitx5 和中文插件
sudo pacman -S fcitx5-im fcitx5-chinese-addons fcitx5-configtool

# 设置 Hyprland 开机自启
echo "exec-once = fcitx5 --replace -d" >> ~/.config/hypr/hyprland.conf

# 修复输入法问题
sudo tee -a /etc/environment << EOF
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS="@im=fcitx"
EOF
```

---

## 🎨 **四、终端美化（Powerlevel10k）**

使用 Powerlevel10k 美化 Zsh 终端，提升用户体验。

```bash
# 安装 Powerlevel10k
git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git \
  ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

# 修改 ~/.zshrc 设置主题
sed -i 's/ZSH_THEME=.*/ZSH_THEME="powerlevel10k\/powerlevel10k"/' ~/.zshrc

# 配置 Powerlevel10k
#p10k configure
```

---

## 🔧 **五、pacman（官方包管理器）**

**pacman** 用于管理 Arch Linux 官方仓库的软件包。以下命令均需 **sudo** 权限。

### 📦 **5.1 安装与卸载软件**

```bash
# 安装单个软件
sudo pacman -S 软件名               # 示例：sudo pacman -S firefox

# 安装多个软件
sudo pacman -S 软件1 软件2          # 示例：sudo pacman -S vim git

# 卸载软件（保留依赖）
sudo pacman -R 软件名               # 示例：sudo pacman -R firefox

# 卸载软件及无用依赖（推荐）
sudo pacman -Rs 软件名              # 示例：sudo pacman -Rs vim

# 强制卸载（忽略依赖，谨慎使用）
sudo pacman -Rdd 软件名
```

### 🔄 **5.2 系统更新**

```bash
# 仅同步软件数据库（不升级）
sudo pacman -Sy

# 升级整个系统（推荐）
sudo pacman -Syu                    # 同步数据库并升级所有软件

# 仅升级特定软件
sudo pacman -S 软件名               # 示例：sudo pacman -S linux 升级内核
```

### 🔍 **5.3 搜索软件**

```bash
# 搜索官方仓库中的软件
pacman -Ss 关键字                   # 示例：pacman -Ss browser

# 搜索已安装的软件
pacman -Qs 关键字                   # 示例：pacman -Qs python

# 查看软件详细信息
pacman -Si 软件名                   # 示例：pacman -Si firefox
```

### 🧹 **5.4 清理与维护**

```bash
# 清理旧版本软件包缓存
sudo pacman -Sc

# 清理所有缓存（释放更多空间）
sudo pacman -Scc

# 删除无用依赖（孤立包）
sudo pacman -Qdtq | sudo pacman -Rs -
```

---

## 🚀 **六、paru（AUR 助手 - yay 的最佳替代品）**

**paru** 是 pacman 的扩展，支持 Arch 用户仓库（AUR）软件，语法与 pacman 一致，且无需 **sudo**。它是 yay 的最佳替代品，Rust 编写，性能更好，默认支持彩色输出和 PKGBUILD 差异显示。

### 📦 **6.1 安装 paru**

```bash
# 安装依赖
sudo pacman -S --needed base-devel git

# 从 AUR 克隆并安装 paru
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```

### 📦 **6.2 安装与更新软件**

```bash
# 安装 AUR 软件
paru -S AUR软件名                   # 示例：paru -S visual-studio-code-bin

# 安装 WPS 办公套件（中文用户常用组合）
paru -S wps-office                  # WPS 主程序
paru -S wps-office-mui-zh-cn        # WPS 中文界面语言包
paru -S ttf-wps-fonts               # WPS 缺失字体补全

# 一键安装常用软件（笔记、浏览器、清理、下载、播放器）
paru -S obsidian                     # 笔记软件 Obsidian
paru -S google-chrome                 # Google Chrome 浏览器
paru -S bleachbit                     # 系统清理工具 BleachBit
paru -S splayer                       # 视频播放器 SPlayer（射手影音）

# 一次性安装多个 AUR 软件
paru -S obsidian google-chrome bleachbit splayer \
       wps-office wps-office-mui-zh-cn ttf-wps-fonts

# 更新系统（官方 + AUR 软件）
paru -Syu

# 仅更新 AUR 软件
paru -Sua
```

### 🔍 **6.3 搜索与管理**

```bash
# 搜索 AUR 和官方仓库
paru -Ss 关键字                      # 示例：paru -Ss browser

# 查看 AUR 软件详情
paru -Si AUR软件名                   # 示例：paru -Si google-chrome

# 查看已安装软件
paru -Qs 关键字                      # 示例：paru -Qs python

# 卸载 AUR 软件（推荐，会删除依赖和配置）
paru -Rns 软件名                      # 示例：paru -Rns obsidian

# 仅卸载软件（保留依赖和配置）
paru -R 软件名                        # 示例：paru -R wps-office
```

### 🛠️ **6.4 高级操作**

```bash
# 下载 PKGBUILD 文件（检查脚本）
paru -G 软件名                       # 示例：paru -G brave-bin

# 手动编译并安装
cd 软件名
makepkg -si

# 清理 AUR 构建缓存
paru -Sc                             # 清理旧版本缓存
paru -Scc                            # 清理全部缓存
paru -Yc                             # 清理未使用的依赖
```

### ⚙️ **6.5 配置 paru（可选）**

```bash
# 编辑配置文件
nano ~/.config/paru/paru.conf

# 常用配置选项（在 [options] 下添加）
#[options]
#SkipReview          # 跳过 PKGBUILD 审查（不推荐）
#RemoveMake          # 安装后自动删除编译依赖
#BottomUp            # 搜索结果排序（新的在底部）
#NewsOnUpgrade       # 更新前显示 Arch 新闻
```

---

## ⚖️ **七、pacman 与 paru 对比**

|**功能**|**pacman**|**paru**|
|---|---|---|
|**软件源**|官方仓库|官方仓库 + AUR|
|**安装命令**|`sudo pacman -S`|`paru -S`（无需 sudo）|
|**更新命令**|`sudo pacman -Syu`|`paru -Syu`|
|**搜索**|`pacman -Ss`（仅官方）|`paru -Ss`（官方 + AUR）|
|**权限**|需要 root 权限|用户权限即可|
|**安全性**|官方包更安全|AUR 包默认审查 PKGBUILD|
|**语言**|C|Rust|
|**特色**|系统核心工具|彩色输出、PKGBUILD 差异显示|

---

## 💡 **八、常见使用场景**

### 🛠️ **8.1 配置开发环境**

```bash
# 安装 Python（官方）
sudo pacman -S python

# 安装 PyCharm 专业版（AUR）
paru -S pycharm-professional
```

### 🧹 **8.2 系统清理**

```bash
# 删除无用依赖
sudo pacman -Qdtq | sudo pacman -Rs -

# 清理缓存（官方 + AUR）
sudo pacman -Scc && paru -Scc && paru -Yc
```

### 🔧 **8.3 修复依赖问题**

```bash
# 重建依赖关系
sudo pacman -Syu --overwrite '*'

# 降级问题软件
paru -S downgrade
downgrade 软件名                    # 选择历史版本
```

---

## 🌟 **九、实用技巧与配置**

### 📋 **9.1 通用技巧**

- **最小化安装**：跳过完整性检查以加快速度：
    
    ```bash
    paru -S 软件名 --mflags "--skipinteg"
    ```
    
- **临时跳过 PKGBUILD 审查**：
    
    ```bash
    paru -S 软件名 --skipreview
    ```
    
- **查看依赖树**：使用 `pactree 软件名`（需安装 `pacman-contrib`）。
- **加速下载**：设置代理（如 Clash）：
    
    ```bash
    export http_proxy="http://127.0.0.1:7890"
    paru -S 软件名
    ```
    

### 🔧 **9.2 AppImage 与字体**

- **提取 AppImage**：
    
    ```bash
    ./MyApp.AppImage --appimage-extract
    运行的软件 --ozone-platform=x11
    ```
    
- **安装字体**：
    
    ```bash
    paru -S ttf-victor-mono
    sudo pacman -S noto-fonts
    ```
    
- **推荐字体**：`JetBrainsMonoNL Nerd Font ExtraBold`

### ⚠️ **9.3 AUR 安全**

- **检查 PKGBUILD**：安装 AUR 包前审查脚本：
    
    ```bash
    paru -G 软件名
    cd 软件名
    cat PKGBUILD  # 手动审查
    ```
    
- **更新开发版 AUR 包**：
    
    ```bash
    paru -Syu --devel
    ```
    

### 🚀 **9.4 从 yay 切换到 paru**

如果你之前用 yay，可以无缝切换：

```bash
# 1. 安装 paru
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si

# 2. 可选：建个别名方便过渡
echo "alias yay=paru" >> ~/.bashrc
source ~/.bashrc

# 3. 之后可以卸载 yay（可选）
paru -Rns yay
```

---

# GKI Kernel for OnePlus Turbo 6 (PLU110)

[![Build GKI Kernel](https://github.com/YOUR_USERNAME/gki_oneplus_PLU110/actions/workflows/build.yml/badge.svg)](https://github.com/YOUR_USERNAME/gki_oneplus_PLU110/actions/workflows/build.yml)

## 设备信息

| 项目 | 信息 |
|------|------|
| 设备名称 | OnePlus Turbo 6 |
| 代号 | PLU110 |
| SoC | MediaTek Dimensity 7400 SUPER |
| 平台 | MT6878 |
| 型号 | CPH2653 |
| 显示屏 | 6.78" AMOLED, 144Hz, 1272x2772 |
| RAM | 12GB LPDDR5 |
| 存储 | 256GB UFS 3.1 |
| 电池 | 8000mAh, 80W 快充 |

## 内核信息

| 项目 | 信息 |
|------|------|
| 内核版本 | Android Common Kernel |
| GKI 版本 | android14-6.1 / android15-6.6 |
| 架构 | ARM64 |
| Compiler | Clang/LLVM |
| KernelSU | 可选支持 |

## 编译方法

### 方法一：GitHub Actions（推荐）

1. Fork 本仓库到你的 GitHub 账号
2. 进入仓库的 **Actions** 标签页
3. 点击 **Build GKI Kernel for OnePlus Turbo 6 (PLU110)**
4. 点击 **Run workflow**
5. 选择内核版本和配置选项
6. 等待编译完成（约1-2小时）
7. 在 Artifacts 或 Releases 中下载内核文件

### 方法二：本地编译

```bash
# 安装依赖 (Ubuntu/Debian)
sudo apt-get install -y bc bison build-essential ccache curl flex \
  g++-multilib gcc-multilib git gnupg gperf \
  lib32ncurses5-dev lib32readline-dev lib32z1-dev liblz4-tool \
  libncurses5 libncurses5-dev libssl-dev libxml2 \
  libxml2-utils lzop rsync schedtool zip zlib1g-dev python3

# 克隆内核源码
git clone --depth=1 --branch android14-6.1 \
  https://android.googlesource.com/kernel/common kernel_common

# 克隆 Clang 工具链
git clone --depth=1 https://android.googlesource.com/platform/prebuilts/clang/host/linux-x86 \
  --branch clang-r416183b1 prebuilts_clang

# 配置内核
cd kernel_common
export PATH=~/prebuilts_clang/bin:$PATH
export CC=clang
export CLANG_TRIPLE=aarch64-linux-gnu-
export CROSS_COMPILE=aarch64-linux-gnu-
make ARCH=arm64 gki_defconfig

# 编译内核
make ARCH=arm64 CC=clang CLANG_TRIPLE=aarch64-linux-gnu- \
  CROSS_COMPILE=aarch64-linux-gnu- -j$(nproc)

# 编译模块
make ARCH=arm64 CC=clang modules -j$(nproc) INSTALL_MOD_PATH=~/modules
```

## 刷入方法

### 前置条件
- 已解锁 Bootloader
- 已安装 ADB 和 Fastboot 工具
- 已备份原始 boot.img

### 刷入步骤

```bash
# 1. 进入 fastboot 模式
# 关机后长按 电源键 + 音量下键

# 2. 刷入内核
fastboot flash boot Image.gz

# 3. 如果有 dtb.img
fastboot flash dtb dtb.img

# 4. 重启
fastboot reboot
```

### 恢复原始内核
```bash
# 使用之前备份的 boot.img
fastboot flash boot boot_backup.img
fastboot reboot
```

## KernelSU 支持

如果在编译时启用了 KernelSU 选项：

1. 编译完成后刷入内核
2. 下载并安装 [KernelSU Manager](https://github.com/tiann/KernelSU/releases)
3. 打开 App 即可管理 root 权限
4. 可以在 App 中安装 KernelSU 模块

## 文件说明

```
output/
├── Image.gz          # GKI 内核镜像
├── modules.tar.gz    # 内核模块
├── *.dtb             # 设备树文件（如果有）
├── dtbo.img          # 设备树覆盖镜像（如果有）
└── build_info.txt    # 构建信息
```

## 目录结构

```
gki_oneplus_PLU110/
├── .github/workflows/
│   └── build.yml              # GitHub Actions 编译工作流
├── kernel/                    # 内核配置文件（可选）
├── README.md
└── .gitignore
```

## 常见问题

### Q: 编译失败怎么办？
A: 检查 GitHub Actions 日志，常见原因：
- 磁盘空间不足（工作流会自动清理）
- 工具链下载失败（检查网络连接）
- 配置选项冲突

### Q: 刷入后无法开机？
A: 
1. 长按电源键 10 秒强制关机
2. 进入 fastboot 模式刷回原始 boot.img
3. 检查是否选择了正确的内核版本

### Q: KernelSU 不工作？
A:
1. 确认编译时启用了 KernelSU 选项
2. 检查是否安装了正确的 KernelSU Manager
3. 查看 KernelSU 官方文档排查问题

## 相关资源

- [Android Common Kernel](https://android.googlesource.com/kernel/common/)
- [GKI Documentation](https://source.android.com/docs/core/architecture/kernel/generic-kernel-image)
- [KernelSU](https://kernelsu.org/)
- [OnePlus Turbo 6 Specs](https://www.oneplus.com/)

## 致谢

- [Google Android Kernel Team](https://source.android.com/)
- [KernelSU Project](https://github.com/tiann/KernelSU)
- [MediaTek Kernel Source](https://github.com/nickel-kernel)

## 许可证

本项目基于 Apache License 2.0 许可证开源。

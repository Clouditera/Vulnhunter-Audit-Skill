# 依赖说明

本文件列出 Vulnhunter-Skill 运行所需的外部依赖及其安装方式。

## cloc

- **用途**：`project-profiler` 技能的扫描脚本通过 `cloc --json` 统计项目的文件数量、代码行数及按语言分布（见 `skills/project-profiler/profile_basic_scan/analyzers/structure.py`）。
- **版本要求**：无硬性版本要求，能输出 `--json` 格式即可（近年发布的版本均支持）。
- **是否必需**：可选但强烈建议。未安装时扫描脚本自动降级——代码统计相关字段返回 0，不会报错中断，但项目画像会缺失代码规模数据。

### Linux 安装

按发行版选择其一：

```bash
# Debian / Ubuntu
sudo apt update && sudo apt install -y cloc

# Fedora / RHEL 8+ / CentOS Stream
sudo dnf install -y cloc

# CentOS 7（需先启用 EPEL）
sudo yum install -y epel-release && sudo yum install -y cloc

# Arch Linux
sudo pacman -S --noconfirm cloc
```

若系统包管理器不可用，也可以通过 npm 安装（需已有 Node.js）：

```bash
npm install -g cloc
```

### Windows 安装

任选其一：

```powershell
# winget（Windows 10/11 自带，推荐）
winget install AlDanial.Cloc

# Scoop
scoop install cloc

# Chocolatey
choco install cloc

# npm（需已有 Node.js）
npm install -g cloc
```

### 验证安装

```bash
cloc --version
```

能正常输出版本号即安装成功。

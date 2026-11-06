> 原文：https://cursor.com/docs/get-started/quickstart

# 快速开始

本指南带你从安装走到在 Cursor 里完成第一次有用的改动。你会登录、让 Cursor 解释代码库、做一处小编辑，并审查结果。

### 安装 Cursor 并登录

下载 Cursor。打开应用并登录。然后选择一个文件夹，从一个小任务开始。

### macOS

- macOS 12（Monterey）及更高版本
- 原生安装包（.dmg）
- 支持 Apple Silicon 与 Intel

### Windows

- Windows 10 及更高版本
- 原生安装包（.exe）

### Linux

**Debian/Ubuntu（推荐）**

```bash
# Add Cursor's GPG key
curl -fsSL https://downloads.cursor.com/keys/anysphere.asc | gpg --dearmor | sudo tee /etc/apt/keyrings/cursor.gpg > /dev/null

# Add the Cursor repository
echo "deb [arch=amd64,arm64 signed-by=/etc/apt/keyrings/cursor.gpg] https://downloads.cursor.com/aptrepo stable main" | sudo tee /etc/apt/sources.list.d/cursor.list > /dev/null

# Update and install
sudo apt update
sudo apt install cursor
```

**RHEL/Fedora**

```bash
# Add Cursor's repository
sudo tee /etc/yum.repos.d/cursor.repo << 'EOF'
[cursor]
name=Cursor
baseurl=https://downloads.cursor.com/yumrepo
enabled=1
gpgcheck=1
gpgkey=https://downloads.cursor.com/keys/anysphere.asc
EOF

# Install Cursor
sudo dnf install cursor
```

**AppImage（便携版）**

从 [cursor.com/downloads](https://cursor.com/downloads) 下载 `.AppImage` 文件，然后：

```bash
chmod +x Cursor-*.AppImage
./Cursor-*.AppImage
```

apt 与 yum 软件包比 AppImage 更合适。它们提供桌面图标、自动更新和 CLI 工具。

### 让 Cursor 解释你的代码库

选定文件夹后，用 Cmd I 打开 Agent。让 Cursor 解释代码库，并指出应该先读的主要部分。

解释这个代码库。指出主要入口、关键模块，以及我在修改之前应该阅读的内容。

Cursor 会搜索你的仓库、阅读相关文件，并总结项目是如何组织在一起的。这是在陌生代码库里建立方向最快的方式之一。

想要更深入的导览？参见[理解你的代码库](https://cursor.com/learn/understanding-your-codebase.md)。

### 做一处小改动

理解项目之后，让 Cursor 建议几处安全的改进。选一个，并让它完成修改。

在这个代码库里建议三处小而安全的改进。说明取舍，并等我选择其中一处。

适合作为第一个任务的，是低风险的事，比如改进一些文案，或修复小的界面问题。

如果你已经知道要改什么，直接提出来，并描述你想要的结果。

### 审查 diff 并验证结果

现在你可以看着 Cursor 工作。diff 视图会显示 Agent 所做的改动。

完成后，审查 diff，并让 Cursor 运行项目已经在用的检查。这可以是测试、类型检查、lint，或本地构建。

想要更稳妥的审查流程？参见[审查与测试代码](https://cursor.com/learn/reviewing-testing.md)。

### 更大的改动使用 Plan Mode

掌握基础之后，更大的改动使用 Plan Mode。当任务跨多个文件、需要调研，或需要在写代码前先获得批准时，它很合适。

在 Agent 输入框中按 Shift+Tab 切换 **Plan Mode**。Cursor 不会立刻写代码，而是会：

1. 研究你的代码库，找出相关文件
2. 就你的需求提出澄清问题
3. 创建详细的实现计划
4. 在开始构建前等待你的批准

更深入的导览见[构建新功能](https://cursor.com/learn/creating-features.md)。

## 后续步骤

### Agent 概述

了解 Agent 的工具与能力

### Rules

为你的项目创建持久指令

### 理解你的代码

了解如何在陌生仓库里建立方向

### 构建新功能

看一套交付更大改动的完整工作流

---

## 站点地图

[所有文档页面概览](/llms.txt)

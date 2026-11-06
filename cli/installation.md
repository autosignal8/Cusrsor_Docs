> 原文：https://cursor.com/docs/cli/installation

# 安装

## 安装

### macOS、Linux 和 Windows（WSL）

用一条命令安装 Cursor CLI：

```bash
curl https://cursor.com/install -fsS | bash
```

### Windows（原生）

使用 PowerShell 在 Windows 上安装 Cursor CLI：

```powershell
irm 'https://cursor.com/install?win32=true' | iex
```

### 验证

安装之后，验证 Cursor CLI 工作正常：

```bash
agent --version
```

## 安装后的设置

1. **把 \~/.local/bin 加入 PATH：**

   对于 bash：

   ```bash
   echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
   source ~/.bashrc
   ```

   对于 zsh：

   ```bash
   echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
   source ~/.zshrc
   ```

2. **开始使用 Cursor Agent：**
   ```bash
   agent
   ```

## 更新

Cursor CLI 默认会尝试自动更新，以确保你始终使用最新版本。

要手动把 Cursor CLI 更新到最新版本：

```bash
agent update
```

---

## 站点地图

[所有文档页面概览](/llms.txt)

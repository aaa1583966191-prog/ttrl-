# AutoDL 配置 Claude Code API 中转完整教程

本教程将指导你在 AutoDL 平台上成功配置 Claude Code 并使用 API 中转服务。

## 目录
- [前置准备](#前置准备)
- [第一步：安装 Node.js](#第一步安装-nodejs)
- [第二步：安装 Claude Code](#第二步安装-claude-code)
- [第三步：配置环境变量](#第三步配置环境变量)
- [第四步：启动 Claude Code](#第四步启动-claude-code)
- [常见问题解决](#常见问题解决)
- [快速启动脚本](#快速启动脚本)

---

## 前置准备

1. **AutoDL 账号**：已登录到 AutoDL 实例终端
2. **API Key**：从 [ikuncode.cc](https://docs.ikuncode.cc/) 获取你的 API Key（格式：`sk-xxx...`）
3. **账户余额**：确保 ikuncode 账户有足够余额

---

## 第一步：安装 Node.js

### 1.1 卸载可能存在问题的 conda nodejs

```bash
# 卸载 conda 安装的 nodejs（避免库冲突）
conda remove nodejs -y
```

### 1.2 使用 NVM 安装 Node.js（推荐）

```bash
# 安装 NVM (Node Version Manager)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# 加载 NVM 环境
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

# 安装 Node.js 20
nvm install 20
nvm use 20
```

### 1.3 验证安装

```bash
node -v   # 应显示：v20.x.x
npm -v    # 应显示：10.x.x
```

**预期输出：**
```
v20.19.6
10.8.2
```

---

## 第二步：安装 Claude Code

### 2.1 全局安装（可选）

```bash
npm install -g @anthropic-ai/claude-code
```

**注意：** 如果 PATH 配置有问题，可以跳过全局安装，直接使用 `npx` 运行（推荐）。

### 2.2 验证安装

```bash
# 使用 npx 验证（推荐，无需配置 PATH）
npx @anthropic-ai/claude-code --version
```

**预期输出：**
```
@anthropic-ai/claude-code@2.0.62
```

---

## 第三步：配置环境变量

### 3.1 添加配置到 ~/.bashrc

```bash
# 编辑 ~/.bashrc 添加环境变量
cat >> ~/.bashrc << 'EOF'

# Claude Code API 中转配置
export ANTHROPIC_AUTH_TOKEN="你的API_KEY"
export ANTHROPIC_BASE_URL="https://api.ikuncode.cc"

# NVM 环境
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
EOF
```

**重要：** 将 `你的API_KEY` 替换为你从 ikuncode 获取的真实 API Key。

### 3.2 使配置生效

```bash
source ~/.bashrc
```

### 3.3 验证环境变量

```bash
echo "Token前15位: ${ANTHROPIC_AUTH_TOKEN:0:15}..."
echo "API URL: $ANTHROPIC_BASE_URL"
```

**预期输出：**
```
Token前15位: sk-E5EeK4wz9ZD...
API URL: https://api.ikuncode.cc
```

---

## 第四步：启动 Claude Code

### 方法一：使用 npx 启动（推荐）

```bash
# 进入工作目录
cd /root/autodl-tmp

# 使用 npx 启动（无需全局安装）
npx @anthropic-ai/claude-code
```

### 方法二：如果全局安装成功

```bash
# 进入工作目录
cd /root/autodl-tmp

# 直接启动
claude-code
```

### 成功启动标志

看到以下界面表示启动成功：

```
Welcome to Claude Code v2.0.62
...
[显示 Claude Code 欢迎界面]
```

---

## 常见问题解决

### 问题 1：`node: symbol lookup error: libnode.so.137: undefined symbol`

**原因：** conda 安装的 Node.js 与系统库冲突

**解决方案：**
```bash
# 卸载 conda 的 nodejs
conda remove nodejs -y

# 使用 NVM 重新安装（参考第一步）
```

### 问题 2：`claude-code: command not found`

**原因：** npm 全局包路径未加入 PATH

**解决方案：** 使用 `npx` 运行（推荐）
```bash
npx @anthropic-ai/claude-code
```

或者手动添加 PATH：
```bash
export PATH="$HOME/.nvm/versions/node/v20.19.6/bin:$PATH"
```

### 问题 3：`Failed to connect to api.anthropic.com: ERR_BAD_REQUEST`

**原因：** 环境变量未正确加载，仍在连接官方 API

**解决方案：**
```bash
# 方法 1：重新加载配置
source ~/.bashrc
npx @anthropic-ai/claude-code

# 方法 2：手动指定环境变量
ANTHROPIC_AUTH_TOKEN="你的KEY" ANTHROPIC_BASE_URL="https://api.ikuncode.cc" npx @anthropic-ai/claude-code

# 方法 3：使用启动脚本（见下方）
```

### 问题 4：heredoc 命令卡住（出现很多 `>` 符号）

**原因：** `cat >> ~/.bashrc << 'EOF'` 命令未正确结束

**解决方案：**
```bash
# 按 Ctrl+C 强制退出
# 或输入 EOF 并回车
EOF
```

---

## 快速启动脚本

为了避免每次都要手动设置环境变量，建议创建一个启动脚本：

### 创建启动脚本

```bash
cat > ~/start-claude.sh << 'EOF'
#!/bin/bash

# 加载 NVM 环境
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

# 设置 Claude Code 环境变量
export ANTHROPIC_AUTH_TOKEN="你的API_KEY"
export ANTHROPIC_BASE_URL="https://api.ikuncode.cc"

# 进入工作目录（可根据需要修改）
cd /root/autodl-tmp

# 启动 Claude Code
npx @anthropic-ai/claude-code
EOF

# 添加执行权限
chmod +x ~/start-claude.sh
```

### 使用启动脚本

以后每次启动只需执行：

```bash
~/start-claude.sh
```

---

## 虚拟环境使用说明

### 在 conda 虚拟环境中使用 Claude Code

**答案：完全可以！**

Claude Code 的文件访问能力与虚拟环境无关，只要满足以下条件：

1. ✓ 环境变量已设置（API 中转配置）
2. ✓ 在终端中启动 Claude Code
3. ✓ 有权限访问目标目录

### 示例：在虚拟环境中使用

```bash
# 激活你的虚拟环境
conda activate your_env

# 确保环境变量已设置
source ~/.bashrc

# 启动 Claude Code
npx @anthropic-ai/claude-code
```

Claude Code 会正常访问和修改当前目录及子目录的所有文件。

---

## 配置检查脚本

创建一个完整的配置检查脚本：

```bash
cat > ~/check-claude-config.sh << 'EOF'
#!/bin/bash

echo "=========================================="
echo "  Claude Code 配置检查"
echo "=========================================="
echo ""

# 检查 Node.js
echo "1. Node.js:"
if command -v node &> /dev/null; then
    echo "   ✓ 已安装: $(node -v)"
else
    echo "   ✗ 未安装"
fi

# 检查 npm
echo "2. npm:"
if command -v npm &> /dev/null; then
    echo "   ✓ 已安装: $(npm -v)"
else
    echo "   ✗ 未安装"
fi

# 检查 NVM
echo "3. NVM:"
if [ -d "$HOME/.nvm" ]; then
    echo "   ✓ 已安装"
else
    echo "   ✗ 未安装"
fi

# 检查环境变量
echo "4. 环境变量:"
if [ -n "$ANTHROPIC_AUTH_TOKEN" ]; then
    echo "   ✓ ANTHROPIC_AUTH_TOKEN: ${ANTHROPIC_AUTH_TOKEN:0:15}..."
else
    echo "   ✗ ANTHROPIC_AUTH_TOKEN 未设置"
fi

if [ -n "$ANTHROPIC_BASE_URL" ]; then
    echo "   ✓ ANTHROPIC_BASE_URL: $ANTHROPIC_BASE_URL"
else
    echo "   ✗ ANTHROPIC_BASE_URL 未设置"
fi

# 检查 Claude Code
echo "5. Claude Code:"
if npx @anthropic-ai/claude-code --version &> /dev/null; then
    echo "   ✓ 可用: $(npx @anthropic-ai/claude-code --version 2>&1)"
else
    echo "   ✗ 不可用"
fi

echo ""
echo "=========================================="
echo "  检查完成"
echo "=========================================="
EOF

chmod +x ~/check-claude-config.sh
```

### 运行检查脚本

```bash
~/check-claude-config.sh
```

---

## 完整安装流程总结

### 一键复制命令（按顺序执行）

```bash
# 1. 卸载 conda nodejs
conda remove nodejs -y

# 2. 安装 NVM
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

# 3. 安装 Node.js 20
nvm install 20
nvm use 20

# 4. 验证安装
node -v && npm -v

# 5. 配置环境变量（注意替换你的 API Key）
cat >> ~/.bashrc << 'EOF'

# Claude Code API 中转配置
export ANTHROPIC_AUTH_TOKEN="替换为你的API_KEY"
export ANTHROPIC_BASE_URL="https://api.ikuncode.cc"

# NVM 环境
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
EOF

# 6. 使配置生效
source ~/.bashrc

# 7. 启动 Claude Code
cd /root/autodl-tmp
npx @anthropic-ai/claude-code
```

---

## 参考资源

- **ikuncode 文档**: https://docs.ikuncode.cc/
- **Claude Code GitHub**: https://github.com/anthropics/claude-code
- **NVM GitHub**: https://github.com/nvm-sh/nvm
- **AutoDL 官网**: https://www.autodl.com/

---

## 更新日志

- **2025-12-09**: 初始版本，基于实际配置经验编写
- 解决了 conda nodejs 库冲突问题
- 解决了环境变量加载问题
- 提供了多种启动方式

---

## 常见使用技巧

### 1. 在特定项目目录启动

```bash
cd /root/autodl-tmp/your-project
npx @anthropic-ai/claude-code
```

### 2. 检查 API 使用情况

登录 ikuncode.cc 查看：
- 余额
- 使用记录
- API 调用统计

### 3. 退出 Claude Code

在 Claude Code 界面中按 `Ctrl+C` 退出

---

## 故障排查清单

遇到问题时，按以下顺序检查：

- [ ] Node.js 和 npm 版本是否正确
- [ ] 环境变量是否已设置
- [ ] API Key 是否正确且有余额
- [ ] 网络连接是否正常
- [ ] 是否在正确的目录下启动

---

**祝使用愉快！如有问题，欢迎参考本教程或查阅官方文档。**

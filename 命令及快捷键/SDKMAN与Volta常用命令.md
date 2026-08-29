# SDKMAN 与 Volta 常用命令

> SDKMAN 用于管理 Java/JVM 工具链；Volta 用于管理 Node.js 工具链。

## 一、快速对照

| 目标 | SDKMAN | Volta |
| --- | --- | --- |
| 查看工具版本 | `sdk version` | `volta --version` |
| 查看可用版本 | `sdk list java` | Volta 无远程版本列表命令 |
| 安装指定版本 | `sdk install java <版本>` | `volta install node@<版本>` |
| 设置全局默认版本 | `sdk default java <版本>` | `volta install node@<版本>` |
| 当前终端临时使用 | `sdk use java <版本>` | `volta run --node <版本> <命令>` |
| 固定项目版本 | `.sdkmanrc` + `sdk env` | `volta pin node@<版本>` |
| 查看当前版本 | `sdk current java` | `volta list --current` |
| 卸载版本 | `sdk uninstall java <版本>` | `volta uninstall node` |

## 二、SDKMAN

### 1. 安装和加载

macOS/Linux 安装：

```bash
curl -s "https://get.sdkman.io" | bash
```

安装后新开终端，或者在当前终端加载：

```bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
```

验证：

```bash
sdk version
```

如果 `~/.sdkman/bin/sdkman-init.sh` 不存在，说明 SDKMAN 尚未成功安装，不能直接执行 `source` 或 `sdk install`。

### 2. 查看候选工具和版本

```bash
# 查看 SDKMAN 支持的所有工具
sdk list

# 查看所有可用 Java 版本
sdk list java

# 查看 Maven 或 Gradle 版本
sdk list maven
sdk list gradle
```

在版本列表中：

- `>`：当前正在使用
- `*`：本机已经安装
- `+`：本地自定义版本
- 按 `q` 退出列表
- 按 `/` 搜索，例如输入 `/21.`

### 3. 安装工具

```bash
# 安装最新稳定版 Java
sdk install java

# 安装指定 Java 版本；版本标识以 sdk list java 的结果为准
sdk install java 21.0.8-tem

# 安装最新稳定版 Maven、Gradle
sdk install maven
sdk install gradle
```

Java 发行版标识常见后缀：

- `-tem`：Eclipse Temurin，通用开发首选
- `-amzn`：Amazon Corretto
- `-zulu`：Azul Zulu
- `-graal`：GraalVM

### 4. 切换和查看版本

```bash
# 仅当前终端切换
sdk use java 17.0.12-tem

# 设置新终端使用的全局默认版本
sdk default java 21.0.8-tem

# 查看当前 Java
sdk current java

# 查看所有当前生效的 SDK
sdk current

# 双重确认环境变量和实际运行版本
java -version
echo "$JAVA_HOME"
mvn -version
```

`sdk use` 只影响当前 shell；关闭终端后失效。`sdk default` 设置后续新 shell 的默认版本。

### 5. 固定项目版本（`.sdkmanrc`）

进入项目根目录：

```bash
# 根据当前工具版本生成 .sdkmanrc
sdk env init
```

典型 `.sdkmanrc`：

```properties
java=21.0.8-tem
maven=3.9.11
```

常用命令：

```bash
# 应用项目指定版本
sdk env

# 安装 .sdkmanrc 中本机缺少的版本
sdk env install

# 离开项目后恢复全局默认版本
sdk env clear
```

需要进入目录时自动切换，可编辑配置：

```bash
sdk config
```

将下面配置改为：

```properties
sdkman_auto_env=true
```

注意：应将 `.sdkmanrc` 提交到 Git 前，先确认团队统一使用 SDKMAN 和相同发行版。

### 6. 更新、卸载和缓存

```bash
# 检查已安装工具是否有新版本
sdk upgrade
sdk upgrade java

# 卸载指定版本
sdk uninstall java 17.0.12-tem

# 刷新候选工具和版本元数据
sdk update

# 更新 SDKMAN 自身
sdk selfupdate

# 清理 SDKMAN 缓存；不要手动删除 ~/.sdkman/tmp
sdk flush

# 获取某个版本的安装目录
sdk home java 21.0.8-tem
```

### 7. SDKMAN 常见问题

```bash
# sdk: command not found
source "$HOME/.sdkman/bin/sdkman-init.sh"

# 查看初始化脚本是否存在
ls -l "$HOME/.sdkman/bin/sdkman-init.sh"

# 查看 zsh 是否配置了 SDKMAN 初始化
grep -n "sdkman-init.sh" "$HOME/.zshrc"

# Java 已切换，但 Maven 似乎还在使用旧 Java
sdk current java
echo "$JAVA_HOME"
mvn -version
```

## 三、Volta

### 1. 安装和初始化

macOS/Linux 安装：

```bash
curl https://get.volta.sh | bash
```

配置当前用户的 shell：

```bash
volta setup
```

验证：

```bash
volta --version
```

### 2. 安装 Node.js 和包管理器

```bash
# 安装最新 LTS Node.js，并设置为全局默认
volta install node@lts

# 安装指定 Node.js 版本
volta install node@22.23.2

# 安装或切换全局默认包管理器
volta install npm@latest
volta install pnpm@latest
volta install yarn@latest

# 一次安装多个工具
volta install node@22 npm@latest pnpm@latest
```

`volta install` 既会下载工具，也会将该版本设为当前用户的全局默认版本。

### 3. 查看当前工具链

```bash
# 查看当前项目实际生效的工具链
volta list
volta list --current

# 查看全局默认工具链
volta list --default

# 查看本机所有已安装工具
volta list all

# 查看某个命令最终指向的真实文件
volta which node
volta which npm
volta which pnpm
```

也可以直接核对：

```bash
node -v
npm -v
pnpm -v
which node
```

### 4. 固定项目版本（`package.json`）

在包含 `package.json` 的项目根目录执行：

```bash
# 固定项目 Node.js 版本
volta pin node@22.23.2

# 固定项目包管理器版本
volta pin npm@11
volta pin pnpm@10
volta pin yarn@4

# 一次固定多个版本
volta pin node@22.23.2 pnpm@10
```

命令会写入 `package.json`：

```json
{
  "volta": {
    "node": "22.23.2",
    "pnpm": "10.0.0"
  }
}
```

进入该项目后，Volta 会自动使用项目版本；离开项目后自动恢复全局默认版本，不需要手动执行切换命令。

### 5. 临时使用指定版本

不修改全局配置和 `package.json`：

```bash
# 临时使用指定 Node.js 执行命令
volta run --node 20 node -v

# 指定 Node.js 和 npm 执行命令
volta run --node 20 --npm 10 npm test

# 指定 Node.js 和 pnpm 执行命令
volta run --node 22 --pnpm 10 pnpm build

# 临时设置环境变量
volta run --node 22 --env NODE_ENV=production npm start
```

### 6. 管理全局 CLI 工具

```bash
# 安装全局 npm CLI
volta install typescript
volta install eslint
volta install @vue/cli

# 安装指定版本
volta install typescript@5.9

# 查看命令位置
volta which tsc
volta which eslint

# 卸载全局 CLI
volta uninstall typescript
volta uninstall eslint
```

用 Volta 管理的全局 CLI 会绑定合适的 Node.js 运行环境，通常不需要再执行 `npm install -g`。

### 7. 下载、卸载和补全

```bash
# 只下载到本机缓存，不改变默认版本
volta fetch node@20
volta fetch pnpm@10

# 卸载全局默认工具
volta uninstall node
volta uninstall pnpm

# 查看帮助
volta help
volta help install
volta help run

# 生成 zsh 自动补全文件
mkdir -p "$HOME/.zfunc"
volta completions zsh --output "$HOME/.zfunc/_volta"
```

如启用 zsh 补全，还需确保 `~/.zshrc` 包含：

```bash
fpath=("$HOME/.zfunc" $fpath)
autoload -Uz compinit
compinit
```

### 8. Volta 常见问题

```bash
# volta: command not found
export VOLTA_HOME="$HOME/.volta"
export PATH="$VOLTA_HOME/bin:$PATH"

# 重新配置 shell
volta setup

# 项目中 Node 版本与预期不符
volta list --current
volta which node
node -v

# 检查 package.json 是否固定了版本
grep -A 5 '"volta"' package.json
```

## 四、推荐工作流

### Java 项目

```bash
cd <项目目录>
sdk list java
sdk install java <项目要求的版本>
sdk use java <项目要求的版本>
sdk env init
java -version
mvn -version
```

### Node.js 项目

```bash
cd <项目目录>
volta pin node@22
volta pin pnpm@10
volta list --current
node -v
pnpm -v
```

## 五、关键区别

1. SDKMAN 的 `sdk use` 负责当前终端临时切换，`sdk default` 负责全局默认，`.sdkmanrc` 负责项目版本。
2. Volta 通常不需要手动切换：`volta install` 设置全局默认，`volta pin` 将项目版本写入 `package.json` 并自动切换。
3. SDKMAN 可以同时管理 Java、Maven、Gradle 等 JVM 工具；Volta 管理 Node.js、npm、pnpm、Yarn 和 npm 全局 CLI。
4. Java 项目除了检查 `java -version`，还应检查 `mvn -version`，确认 Maven 实际使用了正确 JDK。
5. Node.js 项目优先提交 `package.json` 中的 `volta` 配置；Java 项目是否提交 `.sdkmanrc`，应由团队约定。


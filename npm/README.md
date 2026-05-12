# npm — Node Package Manager

## 📌 简介

| 项目 | 内容 |
|------|------|
| **全称** | Node Package Manager |
| **开发者** | Isaac Z. Schlueter（现由 npm, Inc. 维护，后被 GitHub/Microsoft 收购） |
| **首次发布** | 2010 年 |
| **用途** | Node.js 的默认包管理器，用于安装、管理、发布 JavaScript 代码包 |

## 🎯 用来做什么？

- 安装项目依赖（别人写好的代码库）
- 管理项目的版本和依赖关系
- 运行项目中定义的脚本命令
- 发布自己的 JavaScript 包供他人使用

## 🔧 常用命令

### 1. 初始化项目
```bash
npm init -y
```
> 在当前目录生成 `package.json` 文件，记录项目信息和依赖。

### 2. 安装依赖
```bash
# 安装并写入 dependencies（生产环境用）
npm install express

# 安装并写入 devDependencies（开发环境用）
npm install jest --save-dev

# 全局安装（作为命令行工具）
npm install -g typescript

# 根据 package.json 安装所有依赖
npm install
```

### 3. 卸载依赖
```bash
npm uninstall express
npm uninstall -g typescript
```

### 4. 运行脚本
```json
// package.json 中定义
{
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "test": "jest"
  }
}
```
```bash
npm run start
npm run dev
npm test        # test 可以省略 run
```

### 5. 查看信息
```bash
# 查看已安装的包
npm list

# 查看某个包的信息
npm info express

# 检查过时的包
npm outdated

# 更新包
npm update
```

### 6. 其他常用
```bash
# 清除缓存（用于解决奇怪的安装错误）
npm cache clean --force

# 修复依赖问题
npm audit fix
```

## 💡 日常使用示例

### 示例 1：创建一个简单的 Web 服务器
```bash
mkdir my-server
cd my-server
npm init -y
npm install express
```

然后创建 `index.js`：
```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(3000, () => console.log('Server running on http://localhost:3000'));
```

运行：
```bash
node index.js
```

### 示例 2：管理版本号
```bash
# 查看当前版本
node -v

# 给包升级主版本
npm version major   # 1.0.0 → 2.0.0
npm version minor   # 1.0.0 → 1.1.0
npm version patch   # 1.0.0 → 1.0.1
```

### 示例 3：查看全局安装的工具
```bash
npm list -g --depth=0
# 可能会看到 nodemon, typescript, http-server 等
```

## 📚 小技巧

- `npm install` 时可以加 `--save`，但在 npm@5 以后默认就会保存到 package.json，不需要手动加
- `package-lock.json` 是自动生成的，**不要手动编辑它**，它记录了精确的版本号，确保所有人安装的依赖一致
- `npx` 是 npm 自带的工具，可以不安装直接运行包：`npx create-react-app my-app`

## 🔗 参考链接

- [npm 官方文档](https://docs.npmjs.com/)
- [npm 官网](https://www.npmjs.com/)
- [package.json 字段说明](https://docs.npmjs.com/cli/v10/configuring-npm/package-json)

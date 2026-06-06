# plutil — macOS plist 文件检查与转换工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Apple |
| 首次发布 | 2000 年代随 Mac OS X 提供 |
| 用途 | 检查、创建、转换、读取和修改 macOS 的 Property List（.plist）配置文件 |

> ⚠️ **macOS 专属工具**：`plutil` 是 macOS 自带命令，常用于处理 `.plist` 配置文件。Linux 上通常没有同名系统工具。

## 🎯 用来做什么？

- 检查 `.plist` 文件语法是否正确，定位配置文件损坏问题
- 在 XML、二进制、JSON 等 plist 格式之间转换
- 从 plist 中读取某个 key 的值或类型，适合脚本自动化
- 直接插入、替换、删除 plist 中的字段，减少手动编辑 XML 的风险

## 🔧 常用命令

| 命令 | 说明 |
|------|------|
| `plutil -lint file.plist` | 检查 plist 文件语法，成功会输出 `OK` |
| `plutil -p file.plist` | 以人类可读格式打印 plist 内容（不适合机器解析） |
| `plutil -convert json file.plist` | 将 plist 转成 JSON 格式并覆盖原文件 |
| `plutil -convert xml1 -o out.plist file.plist` | 转成 XML plist，输出到指定文件 |
| `plutil -extract key raw file.plist` | 提取指定 key 的原始值 |
| `plutil -type key file.plist` | 查看指定 key 的数据类型 |
| `plutil -insert key -string value file.plist` | 插入字符串字段 |
| `plutil -replace key -bool YES file.plist` | 替换已有字段为布尔值 |
| `plutil -remove key file.plist` | 删除指定字段 |

## 💡 日常使用示例

### 示例 1：创建一个 plist 并检查语法

```bash
# 创建空 plist
$ plutil -create xml1 /tmp/app.plist

# 插入三个字段
$ plutil -insert name -string demo /tmp/app.plist
$ plutil -insert debug -bool YES /tmp/app.plist
$ plutil -insert ports -json '[8000,8001]' /tmp/app.plist

# 检查语法
$ plutil -lint /tmp/app.plist
/tmp/app.plist: OK
```

`-create xml1` 会创建 XML 格式的 plist；`-json` 适合插入数组、字典这类复合值。

### 示例 2：查看 plist 内容、提取值和类型

```bash
$ plutil -p /tmp/app.plist
{
  "debug" => true
  "name" => "demo"
  "ports" => [
    0 => 8000
    1 => 8001
  ]
}

$ plutil -extract name raw /tmp/app.plist
demo

$ plutil -type ports /tmp/app.plist
array
```

注意：`plutil -p` 的输出是给人看的，格式不保证稳定。如果脚本要读取值，优先用 `-extract ... raw`。

### 示例 3：把 plist 转成 JSON 方便查看

```bash
$ plutil -convert json -r -o /tmp/app.json /tmp/app.plist
$ sed -n '1,8p' /tmp/app.json
{
  "debug" : true,
  "name" : "demo",
  "ports" : [
    8000,
    8001
  ]
}
```

`-r` 表示 JSON 输出为可读格式；不加 `-o` 时，`plutil -convert` 会直接覆盖原文件。

## 🚨 常见坑点

| 坑点 | 原因 | 解决方法 |
|------|------|----------|
| `plutil -create xml1 -o file.plist` 报 `No files specified` | `-create` 的语法不是 `-o` 风格 | 用 `plutil -create xml1 file.plist` |
| JSON 文件直接 `plutil -lint app.json` 报错 | macOS `plutil` 默认按 plist 解析，不是通用 JSON 校验器 | 先确认文件确实是 plist/可被 plist 解析的 JSON；普通 JSON 用 `python3 -m json.tool` 或 `jq` |
| `plutil -p` 输出拿去脚本解析不稳定 | 官方说明 `-p` 不是稳定格式 | 脚本中用 `-extract key raw` 或先转 JSON 再用 `jq` |
| 转换后原文件被覆盖 | `-convert` 默认改写原文件 | 加 `-o out.plist` 输出到新文件 |
| 修改系统 plist 没生效 | 很多系统服务会缓存配置 | 修改后按服务要求重启应用、`launchctl kickstart` 或重新登录 |

## 🔗 参考链接

- Apple `plutil` 手册：`man plutil`
- Apple Property List Programming Guide：https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/PropertyLists/Introduction/Introduction.html
